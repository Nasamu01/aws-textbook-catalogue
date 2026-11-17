# Troubleshooting and lessons learned

## Incident 1: EC2 Instance Connect returned an SSH error

**Observed:** AWS browser connection displayed: `Failed to connect to your instance` and `Error establishing an SSH connection to your instance`.

**Initial inspection:** EC2 was `Running` and `3/3 checks passed`, with a public IPv4 address. The associated security group showed only **HTTP / TCP 80** inbound, not SSH.

**Action taken:** Opened **EC2 → Security Groups → textbook-lab-web-sg → Inbound rules → Edit inbound rules**, added **SSH / TCP 22** with a restricted CIDR shown as `18.202.216.48/29` in the configuration screenshot, and retried EC2 Instance Connect.

**Outcome:** A subsequent screenshot shows a successful Amazon Linux 2023 terminal session as `ec2-user`. The screenshots do not independently show the final saved SSH rule; verify it in the console if reproducing the exact setup.

**Lesson:** Instance health and SSH accessibility are different checks. A security group must allow the relevant connection, and network routing, source CIDR, OS firewall and service status must also be correct.

**Caution:** The observed `/29` is not necessarily the correct source for every account, region, connection mode or client. For browser-based EC2 Instance Connect, consult the AWS-published regional EC2 Instance Connect ranges and the actual connection method; do not blindly copy this CIDR.

## Incident 2: `q: command not found`

**Observed:** After checking Apache with `sudo systemctl status httpd`, the shell displayed `-bash: q: command not found`.

**Explanation:** The status pager had already exited. `q` only closes the pager while it is active. The Apache service remained running.

## Incident 3: Default page vs. custom homepage

**Observed:** Browser initially displayed `It works!`, confirming Apache could serve requests over HTTP. After creating `/var/www/html/index.html`, browser displayed the textbook catalogue.

**Lesson:** Apache's document root serves the site content. A successful default page proves the HTTP path, not that custom content is deployed.

## Common checks if the page fails

```bash
sudo systemctl status httpd
sudo ss -lntp | grep ':80'
curl -I http://localhost/
sudo ls -l /var/www/html/index.html
```

- Check the instance's current public IPv4 address.
- Confirm TCP 80 inbound is allowed on the attached security group.
- Confirm the subnet route table has a route to an attached internet gateway and the instance has a public IPv4.
- Check that Apache is active and listening on port 80.
- Do not assume HTTP and HTTPS are interchangeable: this demo does not configure port 443 or TLS.
