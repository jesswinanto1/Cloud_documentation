# AWS VPC Subnetting & CIDR Notation

Class-Based Practice Questions — Interview Preparation Notes

This document consolidates subnet-sizing reference tables, AWS-specific reserved-IP rules, worked subnetting examples, and scenario-based practice questions. Key numbers and formulas are highlighted for quick revision. Sections marked CORRECTED / VERIFIED indicate places where the original notes contained a calculation error that has been fixed below.

---

## 1. Subnet Size → Total IP Addresses

Formula: total addresses in a subnet = 2^(32 − CIDR prefix)

| CIDR (/prefix) | Host Bits (32 − prefix) | Total IP Addresses (2^n) |
|---|---|---|
| /16 | 16 | 65,536 |
| /20 | 12 | 4,096 |
| /22 | 10 | 1,024 |
| /24 | 8 | 256 |
| /26 | 6 | 64 |
| /27 | 5 | 32 |
| /28 | 4 | 16 |
| /29 | 3 | 8 |

**Reverse lookup — "which subnet gives me N addresses?"**

| Total Addresses Needed | Subnet Size |
|---|---|
| 64 | /26 |
| 32 | /27 |
| 16 | /28 |
| 8 | /29 |

---

## 2. Why AWS Reserves 5 IP Addresses per Subnet

**Q: Why does AWS reserve 5 IP addresses in every subnet?**

Traditional on-prem networking reserves only 2 addresses (network address + broadcast address). AWS reserves 5 in every VPC subnet because it needs extra addresses for internal routing, DNS, and future use — even though AWS subnets are software-defined and don't use broadcast the way physical Ethernet segments do.

Usable IPs in AWS = Total IPs in subnet − 5

Example: a /24 subnet normally has 256 addresses → only 251 are usable in AWS.

**Q: Name the five reserved IP addresses in an AWS subnet (example: 10.0.0.0/24)**

| IP Address | Purpose |
|---|---|
| 10.0.0.0 | Network address — identifies the subnet itself |
| 10.0.0.1 | Reserved for the VPC router |
| 10.0.0.2 | Reserved for the AWS-provided DNS server (base + 2) |
| 10.0.0.3 | Reserved by AWS for future use |
| 10.0.0.255 | Network broadcast address (not used by AWS, but still reserved) |

> **Interview tip:** "How many usable IPs in a /28 subnet?" → 16 total − 5 reserved = 11 usable.

**Q: Can you create a /31 subnet in AWS? Why or why not?**

No. AWS VPC subnets must fall between /16 (largest) and /28 (smallest).

- A /31 has only 2 total addresses (normally used for point-to-point links per RFC 3021), which cannot accommodate AWS's 5 reserved addresses.
- AWS enforces /28 as the practical minimum subnet size, since that's the smallest block that still leaves a few usable addresses (16 total − 5 reserved = 11 usable) after the mandatory reservation.
- A /31 or /32 would leave zero or negative usable addresses — unusable for hosting resources.

Bottom line: minimum subnet size = /28, maximum = /16. Anything smaller than /28 (/29, /30, /31) is rejected by the AWS console/API.

---

## 3. Worked Subnetting Examples

**Example A — Divide 10.0.0.0/24 into 4 equal subnets**

2^n ≥ 4 → n = 2 bits borrowed → new prefix = /24 + 2 = /26 (each subnet = 256 ÷ 4 = 64 addresses)

| Subnet | Full Range | Usable Range in AWS (−5) |
|---|---|---|
| 10.0.0.0/26 | 10.0.0.0 – 10.0.0.63 | 10.0.0.4 – 10.0.0.62 (59 usable) |
| 10.0.0.64/26 | 10.0.0.64 – 10.0.0.127 | 10.0.0.68 – 10.0.0.126 (59 usable) |
| 10.0.0.128/26 | 10.0.0.128 – 10.0.0.191 | 10.0.0.132 – 10.0.0.190 (59 usable) |
| 10.0.0.192/26 | 10.0.0.192 – 10.0.0.255 | 10.0.0.196 – 10.0.0.254 (59 usable) |

**Example B — Divide 192.168.1.0/24 into 8 equal subnets**

2^n ≥ 8 → n = 3 bits borrowed → new prefix = /24 + 3 = /27 (each subnet = 256 ÷ 8 = 32 addresses)

**Example C — Divide a /16 network into 16 equal subnets**

2^n ≥ 16 → n = 4 bits borrowed → new prefix = /16 + 4 = /20 (each subnet = 65,536 ÷ 16 = 4,096 addresses)

---

## 4. Scenario-Based Questions

**Q: You need approximately 100 hosts. Which subnet size is appropriate?**

/25 is appropriate — 128 total addresses (126 usable on-prem / 123 usable in AWS), comfortably covering 100 hosts.

**Q: You need 500 hosts. Which subnet size should be selected?**

/23 is appropriate — 512 total addresses (510 usable on-prem / 507 usable in AWS), comfortably covering 500 hosts.

**Q: A company requires 1000 hosts in one subnet. Which CIDR block is suitable?**

> **CORRECTED / VERIFIED**
> The original note answered /10, which is incorrect — a /10 provides over 4 million addresses, far more than needed and not a realistic single-subnet size.
>
> Correct working: 2^n ≥ 1000 → n = 10 host bits → prefix = 32 − 10 = /22. A /22 gives 1,024 total addresses (1,022 usable on-prem / 1,019 usable in AWS), which safely covers 1,000 hosts. (If you want tighter headroom, /21 with 2,046 usable also works, but /22 is the minimal correct fit.)

**Q: You need 5 subnets, each supporting at least 30 hosts. How would you subnet a /24 network?**

2^n ≥ 5 → n = 3 bits borrowed → new prefix = /24 + 3 = /27 → 2^5 = 32 addresses per subnet (30 usable on-prem — meets the ≥30-host requirement).

This creates 8 subnets of /27 each; only the first 5 are needed.

> **CORRECTED / VERIFIED**
> The original note's address ranges contained typos (e.g. "172.16.64./27", overlapping ranges). Corrected list of the first 5 /27 subnets from 172.16.0.0/24:

| # | Subnet | Address Range |
|---|---|---|
| 1 | 172.16.0.0/27 | 172.16.0.0 – 172.16.0.31 |
| 2 | 172.16.0.32/27 | 172.16.0.32 – 172.16.0.63 |
| 3 | 172.16.0.64/27 | 172.16.0.64 – 172.16.0.95 |
| 4 | 172.16.0.96/27 | 172.16.0.96 – 172.16.0.127 |
| 5 | 172.16.0.128/27 | 172.16.0.128 – 172.16.0.159 |

---

## 5. The 5-Step Subnetting Procedure

A repeatable method for dividing any network into smaller, equal subnets — worked through using 192.168.1.0/24 → 4 equal subnets as the running example.

**Step 1 — Bits to borrow**

2^n ≥ number of desired subnets. For 4 subnets: 2^n ≥ 4 → n = 2.

**Step 2 — New CIDR (prefix)**

New CIDR = Original CIDR + n. Here: 24 + 2 = /26.

**Step 3 — Block size (increment)**

Block size = 2^(32 − New CIDR). Here: 2^(32−26) = 2^6 = 64. Subnets increment by 64 in the affected octet.

**Step 4 — Write the network addresses**

Start at the original network address and add the block size repeatedly:

192.168.1.0/26 → 192.168.1.64/26 → 192.168.1.128/26 → 192.168.1.192/26

**Step 5 — Determine IP ranges**

Network ID = starting address. Broadcast ID = 1 less than the next subnet's Network ID. Usable range = everything strictly between Network ID and Broadcast ID.

**Final breakdown table**

| Subnet | Network ID | First Usable IP | Last Usable IP | Broadcast ID |
|---|---|---|---|---|
| 1 | 192.168.1.0 | 192.168.1.1 | 192.168.1.62 | 192.168.1.63 |
| 2 | 192.168.1.64 | 192.168.1.65 | 192.168.1.126 | 192.168.1.127 |
| 3 | 192.168.1.128 | 192.168.1.129 | 192.168.1.190 | 192.168.1.191 |
| 4 | 192.168.1.192 | 192.168.1.193 | 192.168.1.254 | 192.168.1.255 |

> **Note:** If deploying inside AWS, remember AWS automatically reserves the first 3 usable IPs and the last (broadcast) IP of every subnet for its own infrastructure — so usable counts above would each shrink by 5 in a real VPC.
