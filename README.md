# EWB AWS VPC Peering Project

**Author:** Rahul Kumar
**Internship:** AWS Internship (EWB Courses)
**AWS Region:** Asia Pacific (Sydney) — ap-southeast-2
**Completion date:** Not yet complete — in progress as of Oct 26, 2026 evening. App EC2 launches, peering, tests, cleanup and the report PDF are pending Oct 27, 2026.

> Status note (Oct 26, 2026 evening): VPCs, subnets, IGW, route tables and security groups are built and verified from console screenshots. Bastion EC2 was verified Running. Peering, ping/SSH tests, cleanup and the report are NOT yet done — nothing in those sections is claimed yet.
> Do not copy another student's resource IDs, IPs, or screenshots into this repo.
> Never commit a `.pem` key, password, or AWS credential.

## 1. Project Overview

This project demonstrates private communication between two Amazon VPCs using VPC Peering. A public bastion host in VPC A gives SSH access to private application instances in VPC A and VPC B, without giving those application instances public IPs.

Connectivity testing is planned with ICMP ping in both directions, SSH access to the private App-B instance through the bastion host, and a controlled ICMP failure-and-recovery test. Those tests are pending Oct 27, 2026 — not yet performed.

## 2. AWS Region

- Region: Asia Pacific (Sydney)
- Region code: ap-southeast-2

## 3. VPC Configuration

| Resource | Name | Resource ID | CIDR |
|---|---|---|---|
| VPC A | ewb-vpc-a | vpc-04ea62d1372d46a84 | 10.10.0.0/16 |
| VPC B | ewb-vpc-b | vpc-00890b1d02b980903 | 10.20.0.0/16 |

The VPCs use non-overlapping IPv4 CIDR ranges.

## 4. Subnet Configuration

| Subnet | Subnet ID | VPC | CIDR | Availability Zone |
|---|---|---|---|---|
| Public Subnet A | subnet-0cc314d68f2ad64a3 | VPC A | 10.10.1.0/24 | ap-southeast-2a |
| Private Subnet A | subnet-00b4978fd24dc387c | VPC A | 10.10.2.0/24 | ap-southeast-2a |
| Private Subnet B | subnet-0f16a1a0cfddfd671 | VPC B | 10.20.1.0/24 | ap-southeast-2a |

The public subnet auto-assigns a public IPv4 address. The two application subnets do not.

## 5. VPC Peering

- Connection name: ewb-a-b-peer
- Peering Connection ID: Not yet created
- Requester: VPC A
- Accepter: VPC B
- Observed state: Not yet created — pending Oct 27, 2026

An Active peering state alone does not prove successful end-to-end connectivity.

## 6. Route Table Configuration

### Public Route Table A

- Route Table ID: rtb-08c4c23e528ea6b6c
- Associated subnet: Public Subnet A
- `10.10.0.0/16` → local
- `0.0.0.0/0` → Internet Gateway igw-023b05519298e41a5
- `10.20.0.0/16` → VPC Peering connection — planned, NOT yet added (peering not yet created)

### Private Route Table A

- Route Table ID: rtb-0bb077ef11ffcb0c2
- Associated subnet: Private Subnet A
- `10.10.0.0/16` → local
- `10.20.0.0/16` → VPC Peering connection — planned, NOT yet added

### Private Route Table B

- Route Table ID: rtb-073c364f56372b9bc
- Associated subnet: Private Subnet B
- `10.20.0.0/16` → local
- `10.10.0.0/16` → VPC Peering connection — planned, NOT yet added

## 7. Internet Gateway

- Name: ewb-igw-a
- Internet Gateway ID: igw-023b05519298e41a5
- Attached VPC: VPC A only, State: Attached

No NAT gateway was used.

## 8. EC2 Instances

| Instance | Instance ID | Private IP | Public IP | Observed status |
|---|---|---|---|---|
| Bastion | i-06861cf28c434c4d9 | 10.10.1.10 | 3.107.8.12 (restarted Oct 26, 2026, 20:42 IST; earlier public IP 3.25.66.184 was released on stop) | Running, status Initializing at restart screenshot |
| App-A | i-07a0bb1697e20011b | 10.10.2.10 | None | Running, verified Oct 26, 2026, 20:46 IST: VPC A, subnet ewb-a-private, private IP 10.10.2.10, no public IP |
| App-B | i-0614bec9b955b71b9 | 10.20.1.10 | None | Running, verified Oct 26, 2026, 20:49 IST: VPC B, subnet ewb-b-private, private IP 10.20.1.10, no public IP |

Amazon Linux 2023, t3.micro, key pair `ewb-lab`. The key never leaves your laptop. SSH to private instances is via the bastion with ProxyJump.

## 9. Security Groups

### Bastion SG

- Security Group ID: sg-0907e0669898ab430
- Inbound: SSH TCP 22 from 47.15.117.97/32 only
- Never open SSH to 0.0.0.0/0

### App-A SG

- Security Group ID: sg-0e089881b1795deb8
- Inbound: SSH TCP 22 from 10.10.1.10/32
- Inbound: All ICMP-IPv4 from 10.20.1.10/32

### App-B SG

- Security Group ID: sg-08cfa1ce7771efb48
- Inbound: SSH TCP 22 from 10.10.1.10/32
- Inbound: All ICMP-IPv4 from 10.10.2.10/32

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
