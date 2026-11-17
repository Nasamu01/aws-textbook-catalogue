# Security decisions and limitations

## Implemented / observed

- **HTTP inbound:** TCP port 80 from `0.0.0.0/0`, allowing public access to the demo webpage.
- **SSH inbound:** TCP port 22 was added after the first connection failure. An edit screenshot shows source `18.202.216.48/29`. **Confirm the saved rule and whether it matches the chosen EC2 Instance Connect mode.**
- **Root disk:** EBS encryption was selected in the launch form.
- **No application secrets:** The static catalogue has no login, customer database or payment processing.

## Security gaps

1. **Unencrypted HTTP:** Visitors see a browser 'Not secure' indication. Do not submit passwords, personal data or payment information.
2. **Single public EC2 instance:** No load balancer, web application firewall or application-layer protection was configured for this lab.
3. **SSH exposure:** Limit port 22 to the required IP range and close it when not needed. Consider AWS Systems Manager Session Manager after configuring IAM role and management connectivity.
4. **Public IP:** The server is internet-reachable; patch the OS, monitor logs, and avoid installing unnecessary services.
5. **Identity:** Use IAM roles and least privilege for AWS API access; never store AWS access keys in site code or a repository.
6. **TLS:** For a production design, use a domain name, HTTPS, managed certificates and appropriate TLS termination.

## Before publishing on GitHub

- Never commit `.pem`, `.ppk`, access keys, passwords, tokens, `.env`, or customer data.
- Review screenshots for account IDs, personal IP addresses, browser profiles and other identifying information.
- The included website screenshot is a public-facing page only; no account-console screenshots are bundled.
- Resource IDs and the historical public IP in README are for study context, not secrets, but can be generalized if desired.

## Shared responsibility

AWS secures the underlying cloud infrastructure. The customer is responsible for guest OS configuration and patching, security groups, applications, data, IAM configuration, and appropriate encryption settings for this EC2-based deployment.
