# Report skeleton — Rahul_Kumar_EWB_VPC_Peering.pdf

Map every section to the workbook rubric (/100). Fill in only what you actually built and screenshotted. State the region actually used.

## 1. Architecture and IP plan — 15 marks

- Diagram: bastion in public subnet A, App-A in private subnet A, App-B in private subnet B, peering between VPC A and VPC B.
- VPC A: ewb-vpc-a, vpc-04ea62d1372d46a84, 10.10.0.0/16, State: Available, Main route table: rtb-0338c12bdf003083a
- VPC B: ewb-vpc-b, vpc-00890b1d02b980903, 10.20.0.0/16, State: Available, Main route table: rtb-072bd1fc7a492f041
- Public subnet A: subnet-0cc314d68f2ad64a3, 10.10.1.0/24, ap-southeast-2a
- Private subnet A: subnet-00b4978fd24dc387c, 10.10.2.0/24, ap-southeast-2a
- Private subnet B: subnet-0f16a1a0cfddfd671, 10.20.1.0/24, ap-southeast-2a
- Bastion: i-06861cf28c434c4d9, 10.10.1.10, public IP 13.239.235.70 at test time Oct 27, 2026, 18:23 IST (earlier public IPs 3.25.66.184 on Oct 26 and 3.107.8.12 were released on stop/restart); Terminated in cleanup Oct 27, 2026 evening
- App-A: i-07a0bb1697e20011b, 10.10.2.10, no public IP — Running, verified Oct 26, 2026, 20:46 IST
- App-B: i-0614bec9b955b71b9, 10.20.1.10, no public IP — Running, verified Oct 26, 2026, 20:49 IST
- Region actually used: Asia Pacific (Sydney), ap-southeast-2

## 2. Subnets, gateway, routes — 20 marks

- Subnet table with your real subnet IDs and AZs (see Section 1).
- Internet Gateway ewb-igw-a, igw-023b05519298e41a5, attached to VPC A only. No NAT gateway.
- Public route table A: rtb-08c4c23e528ea6b6c, local + 0.0.0.0/0 → IGW verified Oct 26, 2026; peering route 10.20.0.0/16 → pcx-0c41e6177ee14d14b added Oct 27, 2026.
- Private route table A: rtb-0bb077ef11ffcb0c2, local + peering route 10.20.0.0/16 → pcx-0c41e6177ee14d14b added Oct 27, 2026.
- Private route table B: rtb-073c364f56372b9bc, local + peering route 10.10.0.0/16 → pcx-0c41e6177ee14d14b added Oct 27, 2026.
- Screenshot filenames: screenshots/01-route-tables-associations.png, screenshots/02-public-route-table-igw-route.png, screenshots/22-peering-delete-dialog-routes.png (lists all 3 peering routes)

## 3. Peering and both-way routes — 20 marks

- Peering name: ewb-a-b-peer, ID: pcx-0c41e6177ee14d14b, state: Active, verified Oct 27, 2026, 18:55 IST; deleted in cleanup Oct 27, 2026 evening.
- Route in VPC A pointing to VPC B's CIDR via peering, and the return route in VPC B pointing to VPC A's CIDR via peering. Both are required — a one-way route is not enough. Both were added: VPC A tables → 10.20.0.0/16 via peering, VPC B table → 10.10.0.0/16 via peering.
- Screenshot filenames: screenshots/14-peering-active.png, screenshots/22-peering-delete-dialog-routes.png

## 4. Security rules and private SSH — 20 marks

- Bastion SG: sg-0907e0669898ab430, SSH 22 from 47.15.112.103/32 only at test time Oct 27, 2026 (47.15.117.97/32 on Oct 26, updated after the laptop's public IPv4 changed overnight).
- App-A SG: sg-0e089881b1795deb8, SSH from 10.10.1.10/32, ICMP from 10.20.1.10/32.
- App-B SG: sg-08cfa1ce7771efb48, SSH from 10.10.1.10/32, ICMP from 10.10.2.10/32.
- Never SSH or ping open to 0.0.0.0/0.
- SSH into App-B via bastion with agent forwarding (`ssh -A`; ProxyJump was tried Oct 27 and failed on the second hop, agent forwarding worked), key never leaves the laptop.
- Screenshot filename for the SSH session: screenshots/16-ssh-app-b-and-reverse-ping.png (also screenshots/12-ssh-app-a-login.png for App-A)
- Viva notes to remember: the return route is needed for replies; App-B sees the bastion IP as the SSH source; App-B cannot borrow VPC A's internet through peering (peering is non-transitive).

## 5. Test evidence — 15 marks

Capture these in this order, with a screenshot for each:

1. **Before peering — failed ping:** App-A (10.10.2.10) → App-B (10.20.1.10) ping fails, verified Oct 27, 2026, 18:47 IST: 4 packets transmitted, 0 received, 100% packet loss. Screenshot: screenshots/13-failed-ping-app-a-to-app-b.png
2. **After peering — forward ping:** App-A (10.10.2.10) → App-B (10.20.1.10), 4 replies, 0% loss, verified Oct 27, 2026, 19:16 IST, rtt avg 0.291 ms. Screenshot: screenshots/15-successful-ping-forward.png
3. **After peering — reverse ping:** App-B (10.20.1.10) → App-A (10.10.2.10), 4 replies, 0% loss, verified Oct 27, 2026, 19:18 IST, rtt avg 0.192 ms. Screenshot: screenshots/16-ssh-app-b-and-reverse-ping.png
4. **SSH via bastion:** SSH into App-B through the bastion (10.10.1.10 → 10.20.1.10), verified Oct 27, 2026, 19:18 IST, prompt ec2-user@ip-10-20-1-10. Screenshot: screenshots/16-ssh-app-b-and-reverse-ping.png
5. **Controlled failure:** App-B SG ICMP rule removed (sg-08cfa1ce7771efb48, only SSH left) → App-A ping 100% loss, verified Oct 27, 2026, 19:22 IST. Screenshots: screenshots/17-sg-app-b-icmp-removed.png, screenshots/18-controlled-failure-ping-dies.png
6. **Recovery:** ICMP rule restored to 10.10.2.10/32 → App-A ping 4/4, 0% loss, rtt avg 0.237 ms, verified Oct 27, 2026, 19:24 IST. Screenshots: screenshots/19-sg-app-b-icmp-restored.png, screenshots/20-controlled-recovery-ping-revives.png

## 6. Report and cleanup — 10 marks

- Full page-14 cleanup, in order: terminate all EC2 instances → delete peering → delete custom route tables → delete subnets → delete security groups → detach and delete the Internet Gateway → delete both VPCs.
- Check after cleanup: no leftover EBS volumes, snapshots, or Elastic IPs.
- Cleanup proof screenshot filename: screenshots/27-vpcs-cleanup.png and screenshots/31-vpcs-final.png (only default VPC remains); volumes screenshots/28-volumes-empty.png, snapshots screenshots/29-snapshots-empty.png, Elastic IPs screenshots/30-elastic-ips-empty.png — cleanup completed Oct 27, 2026, 19:51–19:55 IST
- Report PDF filename for the repo and the form: `Rahul_Kumar_EWB_VPC_Peering.pdf`

## Do not claim

- Do not claim a region you did not use.
- Do not claim a ping, SSH, or cleanup result without its screenshot.
- Do not claim an Active peering state proves connectivity on its own.
