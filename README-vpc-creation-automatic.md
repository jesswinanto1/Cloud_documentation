# VPC Creation (Automatic) — Task

**Name:** Jesswin Anto J

## Task Overview

Create a VPC (AWS Virtual Private Cloud) with the following configuration:

- **CIDR block: 18.0.0.0/16**
- No IPv6
- 2 Availability Zones — 2 public subnets and 2 private subnets
- Subnet IP ranges: 18.0.1.0/24, 18.0.2.0/24, 18.0.3.0/24, 18.0.4.0/24 (no IPv6 subnets)
- 3 Route Tables — 1 public and 2 private
- Network connections: Public → Internet Gateway (IGW), Private → S3 (VPC Endpoint)

---

**Step 1: selecting region**

**Step 2: searching vpc in search bar and navigate to vpc**

**Step 3: click create vpc**

**Step 4: creating vpc**

1. Vpc setting: vpc and more
2. Name tag: demo_1
3. ipv4 cidr: 18.0.0.0/16
4. ipv6 cidr: no
5. tenancy: default
6. Encryption: none
7. availability zone: 2
8. no. of subnet public: 2
9. no. Of subnet private: 2
10. customize subnets

Public: 1a: 18.0.1.0/24, 1b: 18.0.2.0/24
Private: 1a: 18.0.3.0/24, 1b: 18.0.4.0/24

11. NAT gateway: none
12. vpc endpoint: S3

**Step 5: click create**

**Step 6: verification of vpc**
