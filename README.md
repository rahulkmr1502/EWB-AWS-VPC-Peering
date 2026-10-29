# EWB AWS VPC Peering Project

**Author:** Rahul Kumar
**Internship:** AWS Internship (EWB Courses)
**AWS Region:** Asia Pacific (Sydney) — ap-southeast-2
**Completion date:** Oct 27, 2026 — build, tests and page-14 cleanup all completed and verified from screenshots on Oct 27, 2026.
> Do not copy another student's resource IDs, IPs, or screenshots into this repo.
> Never commit a `.pem` key, password, or AWS credential.

## 1. Project Overview

This project demonstrates private communication between two Amazon VPCs using VPC Peering. A public bastion host in VPC A gives SSH access to private application instances in VPC A and VPC B, without giving those application instances public IPs.

Connectivity was tested with ICMP ping in both directions, SSH access to the private App-B instance through the bastion host, and a controlled ICMP failure-and-recovery test — all completed Oct 27, 2026, then page-14 cleanup was completed the same evening.

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
- Peering Connection ID: pcx-0c41e6177ee14d14b
- Requester: VPC A (vpc-04ea62d1372d46a84, 10.10.0.0/16)
- Accepter: VPC B (vpc-00890b1d02b980903, 10.20.0.0/16)
- Observed state: Active, verified Oct 27, 2026, 18:55 IST — screenshots/14-peering-active.png. Peering routes added Oct 27, 2026 evening (confirmed in the peering-delete dialog, screenshots/22-peering-delete-dialog-routes.png), then the peering was deleted in cleanup.

An Active peering state alone does not prove successful end-to-end connectivity.

## 6. Route Table Configuration

### Public Route Table A

- Route Table ID: rtb-08c4c23e528ea6b6c
- Associated subnet: Public Subnet A
- `10.10.0.0/16` → local
- `0.0.0.0/0` → Internet Gateway igw-023b05519298e41a5
- `10.20.0.0/16` → VPC Peering connection pcx-0c41e6177ee14d14b — added Oct 27, 2026, verified in the peering-delete dialog

### Private Route Table A

- Route Table ID: rtb-0bb077ef11ffcb0c2
- Associated subnet: Private Subnet A
- `10.10.0.0/16` → local
- `10.20.0.0/16` → VPC Peering connection pcx-0c41e6177ee14d14b — added Oct 27, 2026

### Private Route Table B

- Route Table ID: rtb-073c364f56372b9bc
- Associated subnet: Private Subnet B
- `10.20.0.0/16` → local
- `10.10.0.0/16` → VPC Peering connection pcx-0c41e6177ee14d14b — added Oct 27, 2026

## 7. Internet Gateway

- Name: ewb-igw-a
- Internet Gateway ID: igw-023b05519298e41a5
- Attached VPC: VPC A only, State: Attached at build time; detached and deleted in cleanup Oct 27, 2026

No NAT gateway was used.

## 8. EC2 Instances

| Instance | Instance ID | Private IP | Public IP | Observed status |
|---|---|---|---|---|
| Bastion | i-06861cf28c434c4d9 | 10.10.1.10 | 13.239.235.70 (restarted Oct 27, 2026, 18:23 IST; earlier public IPs 3.25.66.184 and 3.107.8.12 were released on stop) | Terminated Oct 27, 2026 evening, after all tests |
| App-A | i-07a0bb1697e20011b | 10.10.2.10 | None | Terminated Oct 27, 2026 evening; was Running, verified Oct 26, 2026, 20:46 IST: VPC A, subnet ewb-a-private, private IP 10.10.2.10, no public IP |
| App-B | i-0614bec9b955b71b9 | 10.20.1.10 | None | Terminated Oct 27, 2026 evening; was Running, verified Oct 26, 2026, 20:49 IST: VPC B, subnet ewb-b-private, private IP 10.20.1.10, no public IP |

Amazon Linux 2023, t3.micro, key pair `ewb-lab`. The key never leaves your laptop. SSH to private instances is via the bastion with agent forwarding (`ssh -A`; ProxyJump was tried Oct 27 and failed on the second hop, agent forwarding worked).

## 9. Security Groups

### Bastion SG

- Security Group ID: sg-0907e0669898ab430
- Inbound: SSH TCP 22 from 47.15.112.103/32 only at test time Oct 27, 2026 (updated that day from 47.15.117.97/32 on Oct 26, after the laptop's public IPv4 changed overnight and SSH hung at "Connecting to port 22")
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

- [x] VPC configuration — screenshots/01-route-tables-associations.png and build screenshots 01–11 (Oct 26, 2026)
- [x] Subnet configuration — screenshots/11-public-subnet-auto-assign.png and build screenshots 01–11
- [x] Internet Gateway — igw-023b05519298e41a5, attached to VPC A only, deleted in cleanup (screenshots/26-igw-deleted.png)
- [x] Public and private route tables — screenshots/01-route-tables-associations.png, 02-public-route-table-igw-route.png, and peering routes confirmed in screenshots/22-peering-delete-dialog-routes.png
- [x] Bastion and application security groups — screenshots/03-security-groups-list.png, 04-bastion-sg.png, 05-app-a-sg.png, 06-app-b-sg.png
- [x] EC2 instance status and private IPs — screenshots/07-instances-list-3-running.png, 08-bastion-details.png, 09-app-a-details.png, 10-app-b-details.png, and Terminated proof screenshots/21-instances-terminated.png
- [x] Failed ping before peering — screenshots/13-failed-ping-app-a-to-app-b.png (Oct 27, 2026, 18:47 IST: App-A 10.10.2.10 → App-B 10.20.1.10, 4 packets transmitted, 0 received, 100% packet loss)
- [x] Successful ping both ways after peering — screenshots/15-successful-ping-forward.png (forward, 0% loss, rtt avg 0.291 ms) and screenshots/16-ssh-app-b-and-reverse-ping.png (reverse, 0% loss, rtt avg 0.192 ms)
- [x] SSH into App-B via bastion — screenshots/16-ssh-app-b-and-reverse-ping.png (prompt ec2-user@ip-10-20-1-10)
- [x] Controlled failure (ping dies) and recovery (ping revives) — screenshots/17-sg-app-b-icmp-removed.png, 18-controlled-failure-ping-dies.png, 19-sg-app-b-icmp-restored.png, 20-controlled-recovery-ping-revives.png
- [x] Cleanup proof — screenshots/21-instances-terminated.png through screenshots/31-vpcs-final.png

## 11. Verification Status and Limitations

Fill this in honestly after the build. Do not tick a box until you have the screenshot.

- [x] Peering connection observed Active — pcx-0c41e6177ee14d14b, Oct 27, 2026, 18:55 IST
- [x] Ping App-A → App-B succeeded, 0% loss — Oct 27, 2026, 19:16 IST: 4 transmitted, 4 received, 0% loss, rtt avg 0.291 ms, screenshots/15-successful-ping-forward.png
- [x] Ping App-B → App-A succeeded, 0% loss — Oct 27, 2026, 19:18 IST: 4 transmitted, 4 received, 0% loss, rtt avg 0.192 ms, screenshots/16-ssh-app-b-and-reverse-ping.png
- [x] SSH to App-B via bastion succeeded — Oct 27, 2026, 19:18 IST: bastion 10.10.1.10 → App-B 10.20.1.10, prompt ec2-user@ip-10-20-1-10, same screenshot
- [x] Controlled failure and recovery completed — Oct 27, 2026, 19:22–19:24 IST: App-B SG ICMP rule removed → App-A ping 100% loss (screenshots/18-controlled-failure-ping-dies.png); rule restored to 10.10.2.10/32 → ping 4/4, 0% loss, rtt avg 0.237 ms (screenshots/20-controlled-recovery-ping-revives.png)
- [x] Full cleanup completed with proof screenshot — Oct 27, 2026, 19:51–19:55 IST: all 3 EC2 Terminated, peering Deleted, 3 custom route tables / 3 project subnets / 3 project SGs / ewb-igw-a / both VPCs deleted; only default VPC (vpc-0e3a49c454a42696f) remains; volumes 0, snapshots 0, Elastic IPs 0 — screenshots/23-route-tables-cleanup.png through screenshots/31-vpcs-final.png

## 12. Cleanup

Page-14 cleanup completed Oct 27, 2026, 19:51–19:55 IST, in order: all 3 EC2 instances Terminated → peering pcx-0c41e6177ee14d14b Deleted → 3 custom route tables deleted (after disassociating their subnets) → 3 project subnets deleted → 3 project security groups deleted → ewb-igw-a detached and deleted → both VPCs deleted. After cleanup: only the default VPC (vpc-0e3a49c454a42696f) remains, with 0 EBS volumes, 0 snapshots, and 0 Elastic IPs. Cleanup proof: screenshots/21-instances-terminated.png through screenshots/31-vpcs-final.png.

## 13. Conclusion

Two private VPCs with non-overlapping CIDRs were connected by peering, proven by a failed ping before peering, successful pings both ways after peering, SSH to the private App-B instance through the bastion, and a controlled ICMP failure and recovery — then fully cleaned up the same evening, with no resources left running.

## Repository Contents

- `README.md` — this file
- `architecture/` — architecture diagram
- `screenshots/` — my own build and test evidence
- `resource-details/` — my own resource inventory
- `Rahul_Kumar_EWB_VPC_Peering.pdf` — my project report (add it here after the build; the EWB form has no separate report-upload field)

## Security note

No private keys, passwords, AWS credentials, or access tokens are committed to this repository.
