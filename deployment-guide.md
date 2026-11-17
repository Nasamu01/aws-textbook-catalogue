# Deployment guide

## 1. Prerequisites

- An AWS account with permissions to use EC2, VPC, security groups and EBS.
- Billing awareness and an AWS Budget set before deploying resources.
- Access to the `eu-west-1` AWS Region (or adjust names/availability zones for your chosen region).
- EC2 Instance Connect capability or a suitable SSH client and securely stored key.

## 2. Networking

In the AWS console, create or choose a VPC such as `textbook-lab-vpc` with CIDR `10.0.0.0/16`. Create a public subnet, e.g. `10.0.0.0/20`, in one Availability Zone. Attach an internet gateway to the VPC and ensure the subnet's associated route table has `0.0.0.0/0` pointing to the internet gateway. Enable a public IPv4 address for the EC2 instance. **Verify these settings rather than assuming that a subnet name makes it public.**

## 3. Launch the instance

In **EC2 → Instances → Launch instances**:

1. Name: `textbook-catalogue-server`.
2. AMI: Amazon Linux 2023 (x86_64).
3. Instance type: `t3.micro` (check current pricing and your own Free Tier eligibility; the label alone does not guarantee no charges).
4. Key pair: select/create one if using SSH. Keep any downloaded private key outside Git.
5. Network: choose your custom VPC, public subnet, and enable auto-assigned public IPv4.
6. Security group: allow TCP 80 from `0.0.0.0/0` for the HTTP demo. Allow TCP 22 **only** from the IP range needed for your administration method, not from the entire internet. EC2 Instance Connect may require AWS service source ranges depending on connection mode.
7. Root EBS: 8 GiB gp3, encrypted, delete on termination (confirm selection).
8. Launch and wait for the instance to show **Running** and healthy status checks.

## 4. Connect

Select the instance → **Connect** → **EC2 Instance Connect** → username `ec2-user` → **Connect**. If it fails, consult [Troubleshooting](troubleshooting.md). A running instance alone does not guarantee network reachability.

## 5. Install and run Apache

```bash
sudo dnf install -y httpd
sudo systemctl start httpd
sudo systemctl enable httpd
sudo systemctl status httpd
```

Press `q` **only while** the `systemctl status` pager is active. If already back at the shell prompt, typing `q` produces `command not found` and does not harm the server.

## 6. Deploy the catalogue

Option A — paste the contents of the repository's `index.html` into a heredoc:

```bash
sudo tee /var/www/html/index.html > /dev/null <<'EOF'
<!-- Replace this line with the COMPLETE contents of index.html from this repository -->
EOF
```

**Important:** The example above is a template, not the finished webpage. Replace the placeholder with the full HTML from [`../index.html`](../index.html).

Option B — upload a copy of `index.html` to the EC2 host using an SSH/SCP workflow, then run:

```bash
sudo cp ~/index.html /var/www/html/index.html
sudo chmod 644 /var/www/html/index.html
```

## 7. Test

```bash
sudo systemctl is-active httpd
curl -I http://localhost/
```

In a browser, visit `http://<EC2_PUBLIC_IPV4>/`. Confirm the Hiddekel-Pishon heading and four sample catalogue cards appear. The observed lab public IPv4 was `54.170.245.50`, but it may change and is not guaranteed to remain assigned.

## 8. Validate and record evidence

- EC2 instance state and health checks
- Instance details (mask account identifiers before publishing)
- Security group inbound rules (check actual source ranges)
- Apache `systemctl status` output
- Browser displaying the catalogue
- Route table / internet gateway, if documenting the public network path

## 9. Stop or remove resources

Follow [Cost and cleanup](cost-and-cleanup.md) immediately after the lab. Do not leave an unused instance running indefinitely.
