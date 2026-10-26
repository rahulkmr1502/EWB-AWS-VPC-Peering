# EWB AWS VPC Peering Project

**Author:** Rahul Kumar
**Internship:** AWS Internship (EWB Courses)
**AWS Region:** [FILL IN THE REGION ACTUALLY USED — do not write Mumbai unless the console actually used ap-south-1]
**Completion date:** [FILL IN AFTER THE BUILD]

> This is a blank template. Nothing below is claimed until you have a screenshot for it.
> Do not copy another student's resource IDs, IPs, or screenshots into this repo.
> Never commit a `.pem` key, password, or AWS credential.

## 1. Project Overview

This project demonstrates private communication between two Amazon VPCs using VPC Peering. A public bastion host in VPC A gives SSH access to private application instances in VPC A and VPC B, without giving those application instances public IPs.

Connectivity was tested with ICMP ping in both directions, SSH access to the private App-B instance through the bastion host, and a controlled ICMP failure-and-recovery test.

[Write 2–3 lines in your own words after the build.]

## 2. AWS Region

- Region: [e.g. Asia Pacific (Mumbai) — only if actually used]
- Region code: [e.g. ap-south-1 — only if actually used]

## 3. VPC Configuration

| Resource | Name | Resource ID | CIDR |
|---|---|---|---|
| VPC A | ewb-vpc-a | [YOUR VPC ID] | 10.10.0.0/16 |
| VPC B | ewb-vpc-b | [YOUR VPC ID] | 10.20.0.0/16 |

The VPCs use non-overlapping IPv4 CIDR ranges.

## 4. Subnet Configuration

| Subnet | Subnet ID | VPC | CIDR | Availability Zone |
|---|---|---|---|---|
| Public Subnet A | [YOUR SUBNET ID] | VPC A | 10.10.1.0/24 | [YOUR AZ, e.g. ap-south-1a if actually used] |
| Private Subnet A | [YOUR SUBNET ID] | VPC A | 10.10.2.0/24 | [YOUR AZ] |
| Private Subnet B | [YOUR SUBNET ID] | VPC B | 10.20.1.0/24 | [YOUR AZ] |

The public subnet auto-assigns a public IPv4 address. The two application subnets do not.

## 5. VPC Peering

- Connection name: ewb-a-b-peer
- Peering Connection ID: [YOUR PCX ID]
- Requester: VPC A
- Accepter: VPC B
- Observed state: [FILL IN ONLY AFTER YOU SEE IT — e.g. Active]

An Active peering state alone does not prove successful end-to-end connectivity.

## 6. Route Table Configuration

### Public Route Table A

- Route Table ID: [YOUR RTB ID]
- Associated subnet: Public Subnet A
- `10.10.0.0/16` → local
- `10.20.0.0/16` → VPC Peering connection
- `0.0.0.0/0` → Internet Gateway [YOUR IGW ID]

### Private Route Table A

- Route Table ID: [YOUR RTB ID]
- Associated subnet: Private Subnet A
- `10.10.0.0/16` → local
- `10.20.0.0/16` → VPC Peering connection

### Private Route Table B

- Route Table ID: [YOUR RTB ID]
- Associated subnet: Private Subnet B
- `10.20.0.0/16` → local
- `10.10.0.0/16` → VPC Peering connection

## 7. Internet Gateway

- Name: ewb-igw-a
- Internet Gateway ID: [YOUR IGW ID]
- Attached VPC: VPC A only

No NAT gateway was used.

## 8. EC2 Instances

| Instance | Instance ID | Private IP | Public IP | Observed status |
|---|---|---|---|---|
| Bastion | [YOUR INSTANCE ID] | 10.10.1.10 | [YOUR BASTION PUBLIC IP] | [e.g. Running] |
| App-A | [YOUR INSTANCE ID] | 10.10.2.10 | None | [e.g. Running] |
| App-B | [YOUR INSTANCE ID] | 10.20.1.10 | None | [e.g. Running] |

Amazon Linux 2023, t3.micro, key pair `ewb-lab`. The key never leaves your laptop. SSH to private instances is via the bastion with ProxyJump.

## 9. Security Groups

### Bastion SG

- Security Group ID: [YOUR SG ID]
- Inbound: SSH TCP 22 from [YOUR OWN PUBLIC IP]/32 only
- Never open SSH to 0.0.0.0/0

### App-A SG

- Security Group ID: [YOUR SG ID]
- Inbound: SSH TCP 22 from [BASTION PRIVATE IP]/32
- Inbound: All ICMP-IPv4 from [APP-B PRIVATE IP]/32

### App-B SG

- Security Group ID: [YOUR SG ID]
- Inbound: SSH TCP 22 from [BASTION PRIVATE IP]/32
- Inbound: All ICMP-IPv4 from [APP-A PRIVATE IP]/32

## 10. Evidence and Screenshots

Put your own screenshots in `screenshots/` and list them here with the exact filenames you used. See `SCREENSHOT_AND_CLEANUP_CHECKLIST.md` for the order to capture them in.

- [ ] VPC configuration
- [ ] Subnet configuration
- [ ] Internet Gateway
- [ ] Public and private route tables
- [ ] Bastion and application security groups
- [ ] EC2 instance status and private IPs
- [ ] Failed ping before peering
- [ ] Successful ping both ways after peering
- [ ] SSH into App-B via bastion
- [ ] Controlled failure (ping dies) and recovery (ping revives)
- [ ] Cleanup proof

## 11. Verification Status and Limitations

Fill this in honestly after the build. Do not tick a box until you have the screenshot.

- [ ] Peering connection observed Active
- [ ] Ping App-A → App-B succeeded, 0% loss
- [ ] Ping App-B → App-A succeeded, 0% loss
- [ ] SSH to App-B via bastion succeeded
- [ ] Controlled failure and recovery completed
- [ ] Full cleanup completed with proof screenshot

## 12. Cleanup

[Fill in after page-14 cleanup: all EC2 instances terminated, no leftover EBS volumes, snapshots, or Elastic IPs, and peering, route tables, subnets, security groups, Internet Gateway, and both VPCs deleted. Attach the cleanup proof screenshot filename here.]

## 13. Conclusion

[Write 2–3 lines in your own words after the build and cleanup.]

## Repository Contents

- `README.md` — this file
- `architecture/` — architecture diagram
- `screenshots/` — your own build and test evidence
- `resource-details/` — your own resource inventory
- `Rahul_Kumar_EWB_VPC_Peering.pdf` — your project report (add it here after the build; the EWB form has no separate report-upload field)

## Security note

No private keys, passwords, AWS credentials, or access tokens are committed to this repository.
