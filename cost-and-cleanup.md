# Cost management and cleanup

## Resources that may incur charges

| Item | Billing consideration |
|---|---|
| EC2 `t3.micro` | Compute usage while running; eligibility for any free allowance depends on account, region and current program rules |
| EBS gp3 8 GiB | Storage may be charged even if the instance is stopped |
| Public IPv4 | AWS generally charges for public IPv4 addresses, including those attached to running instances; check current regional rates |
| Data transfer | Outbound internet data may incur usage charges depending on allowance and volume |
| Snapshots / extras | Charged if created; none are required for this lab |
| NAT gateway | Not required for this simple public-subnet deployment; avoid creating one just for this exercise because it can incur hourly and processing charges |

**No exact cost estimate is asserted here:** rates, credits, taxes, free allowances and usage vary. Check the [AWS Pricing Calculator](https://calculator.aws/) and AWS Billing console for current pricing in your region.

## Set cost controls

1. Open **Billing and Cost Management → Budgets**.
2. Create a **Cost budget** with a low monthly threshold appropriate to your account.
3. Add an email alert for actual and forecast spend.
4. Check **Cost Explorer** and **Bills** after deploying. Budget alerts do not automatically prevent spending.
5. Optionally create a CloudWatch alarm for EC2 CPU or status checks; evaluate monitoring charges.

## Stop vs terminate

- **Stop:** Compute billing generally stops, but EBS volumes remain and may incur storage charges. Auto-assigned public IPv4 usually changes after stop/start. Other charges may remain.
- **Terminate:** Ends the instance; root EBS volume is deleted only if `Delete on termination` is enabled. Check for retained EBS volumes, snapshots, Elastic IPs, load balancers and other resources.
- **Security groups and VPCs:** These resources usually have no direct hourly charge, but associated services such as NAT gateways, endpoints or public IPv4 addresses may cost money.

## Safe cleanup checklist

1. Save any files or evidence you need from EC2.
2. Open **EC2 → Instances**, select the lab instance, and choose **Instance state → Terminate instance** when finished permanently.
3. Confirm **EC2 → Volumes** has no unexpected retained billable EBS volumes.
4. Check **Snapshots**, **Elastic IPs / public IPv4**, **Load Balancers**, and **NAT Gateways** for leftover billable resources.
5. If the VPC was created solely for the lab, remove dependent resources first and delete it only after verifying nothing else uses it.
6. Recheck **Billing → Bills / Cost Explorer** after charges have had time to appear.

**Important:** Do not terminate an instance if you still need files stored on it and have not backed them up.
