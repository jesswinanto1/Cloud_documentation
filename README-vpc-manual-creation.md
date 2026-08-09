# Manual VPC Creation on AWS

**Name:** Jesswin Anto J
**Date:** 16/07/2026

## Objective

To manually create a custom Virtual Private Cloud (VPC) named demo-vpc-05 on AWS, configure four subnets (two public and two private) across multiple Availability Zones, set up public and private route tables, attach and configure an Internet Gateway for public subnet internet access, create a VPC Gateway Endpoint for private subnet connectivity to Amazon S3, and verify the complete network configuration.

---

## Step 1: Selecting the Region

Select the required AWS region. For this lab, the region chosen is Asia Pacific (Mumbai) — ap-south-1.

## Step 2: Navigating to the VPC Service

In the AWS Management Console search bar, type "VPC" and select the first matching option, VPC, from the results.

## Step 3: Initiating VPC Creation

Once the VPC page opens, click Create VPC.

**Note:** Ensure that no more than 5 VPCs are created per account/region, as AWS may charge for additional VPCs beyond the default limit.

## Step 4: VPC Creation — Configuration

### VPC Settings
- **Name tag (optional):** demo-vpc-05
- **IPv4 CIDR block:** IPv4 CIDR manual input
- **IPv4 CIDR:** 24.0.0.0/16
- **IPv6 CIDR block:** No IPv6 CIDR block
- **Tenancy:** default
- **VPC encryption control ($):** none

**Click: Create VPC**

*Figure 1: VPC creation settings (arrow → Create VPC button)*
*Figure 2: VPC successfully created*

---

## Step 5: Creating Subnets

Here are all the selected values from the AWS Create Subnet page:

### VPC
- **VPC ID:** vpc-0a59a362598c19a70 (demo-vpc-05)
- **IPv4 CIDR:** 24.0.0.0/16

### Subnet 1 of 4
- **Subnet name:** demo-vpc-05-subnet-public-01
- **Availability Zone:** Asia Pacific (Mumbai) / aps1-az1 (ap-south-1a)
- **IPv4 VPC CIDR block:** 24.0.0.0/16
- **IPv4 subnet CIDR block:** 24.0.1.0/24
- **Tag:** Key: Name, Value: demo-vpc-05-subnet-public-01

### Subnet 2 of 4
- **Subnet name:** demo-vpc-05-subnet-public-02
- **Availability Zone:** Asia Pacific (Mumbai) / aps1-az3 (ap-south-1b)
- **IPv4 VPC CIDR block:** 24.0.0.0/16
- **IPv4 subnet CIDR block:** 24.0.2.0/24
- **Tag:** Key: Name, Value: demo-vpc-05-subnet-public-02

### Subnet 3 of 4
- **Subnet name:** demo-vpc-05-subnet-private-01
- **Availability Zone:** Asia Pacific (Mumbai) / aps1-az1 (ap-south-1a)
- **IPv4 VPC CIDR block:** 24.0.0.0/16
- **IPv4 subnet CIDR block:** 24.0.3.0/24
- **Tag:** Key: Name, Value: demo-vpc-05-subnet-private-01

### Subnet 4 of 4
- **Subnet name:** demo-vpc-05-subnet-private-02
- **Availability Zone:** Asia Pacific (Mumbai) / aps1-az3 (ap-south-1b)
- **IPv4 VPC CIDR block:** 24.0.0.0/16
- **IPv4 subnet CIDR block:** 24.0.4.0/24
- **Tag:** Key: Name, Value: demo-vpc-05-subnet-private-02

*Figure 3: Subnet configuration summary (arrows → IPv4 subnet CIDR block for each of the 4 subnets)*
*Figure 4: Subnets created*

---

## Step 6: Creating Route Tables

### 6.1 Create Private Route Table

**Route table settings:**
- **Name:** demo-vpc-05-Route-table-private
- **VPC:** vpc-0a59a362598c19a70 (demo-vpc-05)

**Tags:**
- **Tag:** Key: Name, Value: demo-vpc-05-Route-table-private

*Figure 5: Private route table created (arrow → VPC selection)*

### 6.2 Create Public Route Table

**Route table settings:**
- **Name:** demo-vpc-05-Route-table-public
- **VPC:** vpc-0a59a362598c19a70 (demo-vpc-05)

**Tags:**
- **Tag:** Key: Name, Value: demo-vpc-05-Route-table-public

*Figure 6: Public route table settings*
*Figure 7: Public route table created*

---

## Step 7: Creating and Attaching the Internet Gateway

**Internet gateway settings:**
- **Name tag:** demo-vpc-05-Internet-gateway

**Tags:**
- **Tag:** Key: Name, Value: demo-vpc-05-Internet-gateway

*Figure 8: Internet gateway settings (arrow → Create internet gateway button)*
*Figure 9: Internet gateway created*

### Attaching the Internet Gateway to the VPC

**Step 1:**

*Figure 10: Internet gateway details*

**Step 2:**

*Figure 11: Attach to VPC (arrow → Attach internet gateway button)*

**Step 3:**

*Figure 12: Internet gateway attached to VPC*

---

## Step 8: Subnet Route Table Association

Associating the appropriate route tables with each subnet:

*Figure 13: Route table association — subnet 1 (arrow → Edit subnet associations)*
*Figure 14: Route table association — subnet 2 (arrow → Edit subnet associations)*
*Figure 15: Route table association — subnet 3 (arrow → Edit subnet associations)*
*Figure 16: Route table association — subnet 4 (arrow → Edit subnet associations)*

---

## Step 9: Assigning Internet Gateway Access to the Public Subnet

- **Destination:** 0.0.0.0/0
- **Target:** Internet Gateway → select public subnet

*Figure 17: Public route configured with Internet Gateway (arrow → 0.0.0.0/0 route to IGW)*

---

## Step 10: Creating a VPC Endpoint for the Private Subnet

**Endpoint settings:**
- **Name tag:** demo-vpc-05-Route-Endpoint
- **Type:** AWS services

**Services:**
- **Service Name:** com.amazonaws.ap-south-1.s3
- **Owner:** amazon
- **Type:** Gateway

**Network settings:**
- **VPC:** vpc-0a59a362598c19a70 (demo-vpc-05)

**Route tables:**
- **Selected:** demo-vpc-05-Route-table-private — rtb-0740e63e4466cb689 (demo-vpc-0...) — Main: No — Associated: 2 subnets

**Policy:**
- **Access:** Full access

**Tags:**
- **Tag:** Key: Name, Value: demo-vpc-05-Route-Endpoint

*Figure 18: VPC endpoint configuration (arrow → private route table selected)*

---

## Step 11: Verification

Final verification of the complete VPC setup, including subnets, route tables, internet gateway, and endpoint.

*Figure 19: Final VPC resource map / verification (arrows → Route tables & Network Connections)*
