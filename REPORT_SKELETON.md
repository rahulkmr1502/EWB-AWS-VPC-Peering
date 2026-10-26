# Report skeleton — Rahul_Kumar_EWB_VPC_Peering.pdf

Map every section to the workbook rubric (/100). Fill in only what you actually built and screenshotted. State the region actually used.

## 1. Architecture and IP plan — 15 marks

- Diagram: bastion in public subnet A, App-A in private subnet A, App-B in private subnet B, peering between VPC A and VPC B.
- VPC A: ewb-vpc-a, vpc-04ea62d1372d46a84, 10.10.0.0/16, State: Available, Main route table: rtb-0338c12bdf003083a
- VPC B: ewb-vpc-b, vpc-00890b1d02b980903, 10.20.0.0/16, State: Available, Main route table: rtb-072bd1fc7a492f041
- Public subnet A: subnet-0cc314d68f2ad64a3, 10.10.1.0/24, ap-southeast-2a
- Private subnet A: subnet-00b4978fd24dc387c, 10.10.2.0/24, ap-southeast-2a
- Private subnet B: subnet-0f16a1a0cfddfd671, 10.20.1.0/24, ap-southeast-2a
- Bastion: i-06861cf28c434c4d9, 10.10.1.10, public IP 3.25.66.184 at verification Oct 26, 2026, 19:58 IST
- App-A: i-07a0bb1697e20011b, 10.10.2.10, no public IP — Running, verified Oct 26, 2026, 20:46 IST
- App-B: i-0614bec9b955b71b9, 10.20.1.10, no public IP — Running, verified Oct 26, 2026, 20:49 IST
- Region actually used: Asia Pacific (Sydney), ap-southeast-2

## 2. Subnets, gateway, routes — 20 marks

- Subnet table with your real subnet IDs and AZs (see Section 1).
- Internet Gateway ewb-igw-a, igw-023b05519298e41a5, attached to VPC A only. No NAT gateway.
- Public route table A: rtb-08c4c23e528ea6b6c, local + 0.0.0.0/0 → IGW verified Oct 26, 2026. Peering route 10.20.0.0/16 → peering is planned, NOT yet added.
- Private route table A: rtb-0bb077ef11ffcb0c2, local only at present. Peering route planned, NOT yet added.
- Private route table B: rtb-073c364f56372b9bc, local only at present. Peering route planned, NOT yet added.
- Screenshot filenames: [LIST THEM — screenshots to be uploaded Oct 27, 2026]

## 3. Peering and both-way routes — 20 marks

- Peering name: ewb-a-b-peer, ID: Not yet created, state: Not yet created — pending Oct 27, 2026
- Route in VPC A pointing to VPC B's CIDR via peering, and the return route in VPC B pointing to VPC A's CIDR via peering. Both are required — a one-way route is not enough.
- Screenshot filenames: [LIST THEM]

## 4. Security rules and private SSH — 20 marks

- Bastion SG: sg-0907e0669898ab430, SSH 22 from 47.15.117.97/32 only.
- App-A SG: sg-0e089881b1795deb8, SSH from 10.10.1.10/32, ICMP from 10.20.1.10/32.
- App-B SG: sg-08cfa1ce7771efb48, SSH from 10.10.1.10/32, ICMP from 10.10.2.10/32.
- Never SSH or ping open to 0.0.0.0/0.
- SSH into App-B via bastion with ProxyJump, key never leaves the laptop.
- Screenshot filename for the SSH session: [FILL IN]
- Viva notes to remember: the return route is needed for replies; App-B sees the bastion IP as the SSH source; App-B cannot borrow VPC A's internet through peering (peering is non-transitive).

## 5. Test evidence — 15 marks

Capture these in this order, with a screenshot for each:

1. **Before peering — failed ping:** App-A → App-B ping fails. Screenshot: [FILENAME]
2. **After peering — forward ping:** App-A (10.10.2.10) → App-B (10.20.1.10), [e.g. 4 replies, 0% loss]. Screenshot: [FILENAME]
3. **After peering — reverse ping:** App-B → App-A, [replies / loss]. Screenshot: [FILENAME]
4. **SSH via bastion:** SSH into App-B through the bastion. Screenshot: [FILENAME]
5. **Controlled failure:** remove one ping rule → ping dies. Screenshot: [FILENAME]
6. **Recovery:** restore the rule → ping revives. Screenshot: [FILENAME]

## 6. Report and cleanup — 10 marks

- Full page-14 cleanup, in order: terminate all EC2 instances → delete peering → delete custom route tables → delete subnets → delete security groups → detach and delete the Internet Gateway → delete both VPCs.
- Check after cleanup: no leftover EBS volumes, snapshots, or Elastic IPs.
- Cleanup proof screenshot filename: [FILL IN]
- Report PDF filename for the repo and the form: `Rahul_Kumar_EWB_VPC_Peering.pdf`

## Do not claim

- Do not claim a region you did not use.
- Do not claim a ping, SSH, or cleanup result without its screenshot.
- Do not claim an Active peering state proves connectivity on its own.
