# Report skeleton — Rahul_Kumar_EWB_VPC_Peering.pdf

Map every section to the workbook rubric (/100). Fill in only what you actually built and screenshotted. State the region actually used.

## 1. Architecture and IP plan — 15 marks

- Diagram: bastion in public subnet A, App-A in private subnet A, App-B in private subnet B, peering between VPC A and VPC B.
- VPC A: ewb-vpc-a, 10.10.0.0/16
- VPC B: ewb-vpc-b, 10.20.0.0/16
- Public subnet A: 10.10.1.0/24
- Private subnet A: 10.10.2.0/24
- Private subnet B: 10.20.1.0/24
- Bastion: 10.10.1.10 (public IP: [YOURS])
- App-A: 10.10.2.10, no public IP
- App-B: 10.20.1.10, no public IP
- Region actually used: [FILL IN]

## 2. Subnets, gateway, routes — 20 marks

- Subnet table with your real subnet IDs and AZs.
- Internet Gateway ewb-igw-a, attached to VPC A only. No NAT gateway.
- Public route table A: local + 0.0.0.0/0 → IGW + 10.20.0.0/16 → peering.
- Private route table A: local + 10.20.0.0/16 → peering.
- Private route table B: local + 10.10.0.0/16 → peering.
- Screenshot filenames: [LIST THEM]

## 3. Peering and both-way routes — 20 marks

- Peering name: ewb-a-b-peer, ID: [YOUR PCX ID], state: [OBSERVED STATE]
- Route in VPC A pointing to VPC B's CIDR via peering, and the return route in VPC B pointing to VPC A's CIDR via peering. Both are required — a one-way route is not enough.
- Screenshot filenames: [LIST THEM]

## 4. Security rules and private SSH — 20 marks

- Bastion SG: SSH 22 from your own IP /32 only.
- App-A SG: SSH from bastion private IP /32, ICMP from App-B IP /32.
- App-B SG: SSH from bastion private IP /32, ICMP from App-A IP /32.
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
