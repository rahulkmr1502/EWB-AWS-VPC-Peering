# Screenshot and cleanup checklist

Work through this in order, one screenshot at each step. The report is written from these screenshots afterwards.

## Important — current cost limit (26 Oct 2026)

Rahul's current limit is: $1 card verification hold only, NO usage charges.
Do not launch EC2 on the old account under this limit. Use this checklist only once a $0 route is confirmed — a credits-funded reactivated account, or a free sandbox that allows custom VPC/EC2 creation.

## Build order

- [ ] 1. VPC A — ewb-vpc-a, 10.10.0.0/16 — screenshot
- [ ] 2. VPC B — ewb-vpc-b, 10.20.0.0/16 — screenshot
- [ ] 3. Subnets — public A 10.10.1.0/24, private A 10.10.2.0/24, private B 10.20.1.0/24 — screenshot
- [ ] 4. Internet Gateway — ewb-igw-a, attached to VPC A only — screenshot
- [ ] 5. Route tables — public A, private A, private B — screenshot each
- [ ] 6. Security groups — bastion, App-A, App-B, never 0.0.0.0/0 — screenshot each
- [ ] 7. EC2 instances — bastion 10.10.1.10, App-A 10.10.2.10, App-B 10.20.1.10 — screenshot status and private IPs
- [ ] 8. FAILED PING BEFORE PEERING — App-A → App-B ping fails — screenshot (this is graded evidence; capture it before peering exists)
- [ ] 9. Peering + routes both ways — ewb-a-b-peer, Active, plus both return routes — screenshot
- [ ] 10. SUCCESSFUL PING BOTH WAYS — App-A → App-B and App-B → App-A, 0% loss — screenshot each
- [ ] 11. SSH VIA BASTION — SSH into App-B through the bastion with ProxyJump — screenshot
- [ ] 12. CONTROLLED FAILURE — remove one ping rule → ping dies — screenshot
- [ ] 13. RECOVERY — restore the rule → ping revives — screenshot

## Page-14 full cleanup (not optional, graded)

Do this immediately after the build, in this order:

- [ ] Terminate all EC2 instances (bastion, App-A, App-B)
- [ ] Delete the peering connection
- [ ] Delete custom route tables
- [ ] Delete subnets
- [ ] Delete security groups
- [ ] Detach and delete the Internet Gateway
- [ ] Delete both VPCs
- [ ] Check: no leftover EBS volumes, snapshots, or Elastic IPs
- [ ] Cleanup proof screenshot

## Hard rules

- One account, one region, for the whole build.
- No NAT gateway.
- Never open SSH or ping to 0.0.0.0/0.
- App-A and App-B never get public IPs.
- Key pair `ewb-lab` never leaves the laptop.
- If a sandbox is used, the session auto-cleans after about 8 hours — build in one continuous block and capture screenshots as you go.
