# Launch Template, Target Group, Application Load Balancer & Auto Scaling Group — Documentation

**By:** Jesswin Anto J
**Date:** 28 July 2026

## Objective

Design and deploy a highly available, self-healing web application architecture on AWS. The task involves provisioning two EC2 web server instances, registering them under a Target Group, distributing incoming traffic through an Application Load Balancer, and configuring a Launch Template together with an Auto Scaling Group so that the number of running instances automatically adjusts based on CPU utilization.

> **NOTE**
> This documentation captures every configuration screen exactly as selected during the AWS Console walkthrough, in the sequence the resources were created: EC2 instances → Target Group → Load Balancer → Launch Template → Auto Scaling Group.

---

## Step 1: EC2 Instances

Two Ubuntu-based EC2 instances were launched to act as backend web servers behind the load balancer.

### Instance 1 — web-server-LB-1

**Name and Tags**
- **Name:** web-server-LB-1

**Application and OS Image (AMI)**
- **Quick Start OS:** Ubuntu
- **AMI:** Ubuntu Server 26.04 LTS (HVM), SSD Volume Type — Free tier eligible
- **AMI ID:** ami-01a00762f46d584a1
- **Architecture:** 64-bit (x86)
- **Creation Date:** 2026-06-04
- **Username:** ubuntu

**Instance Type**
- **Instance type:** t3.micro

**Key Pair (Login)**
- **Key pair name:** KP-web-server-LB-1

**Network Settings**
- **VPC:** vpc-06a5370c19a64dfce (Demo-1-vpc) — 10.0.0.0/16
- **Subnet:** subnet-01f4692fbcaf0d945 — Demo-1-subnet-public1-ap-south-1a (AZ: ap-south-1a / aps1-az1, CIDR: 10.0.1.0/24)
- **Auto-assign public IP:** Enable
- **Firewall (security group):** Create new security group — web-server-LB-1
- **Description:** web-server-LB-1 created 2026-07-28T13:11:08.301Z

**Inbound Security Group Rules**
- **Rule 1:** SSH, TCP, Port 22, Source: Anywhere (0.0.0.0/0)
- **Rule 2:** HTTP, TCP, Port 80, Source: Custom (0.0.0.0/0)
- **Rule 3:** HTTPS, TCP, Port 443, Source: Custom (0.0.0.0/0)

> **NOTE**
> Opening SSH (port 22) to 0.0.0.0/0 is acceptable for a lab/demo environment, but for production it should be restricted to a specific, trusted IP range.

**Storage**
- **Root volume:** 1 x 8 GiB, gp3, 3000 IOPS, not encrypted
- **File systems:** None

**Advanced Details**
- **Hostname type:** IP name
- **DNS hostname:** Enable IP name IPv4 (A record) DNS requests
- **Shutdown behavior:** Stop
- **Metadata version:** V2 only (token required)
- **Metadata hop limit:** 2

**User Data (Bootstrap Script)**
```bash
#!/bin/bash
# Update packages
apt-get update -y
# Install Nginx
apt-get install -y nginx
# Enable and start Nginx
systemctl enable nginx
systemctl start nginx
# Get hostname
HOSTNAME=$(hostname)
...
```

> **NOTE**
> The user-data script updates packages, installs and starts Nginx, and captures the instance hostname — this hostname is later displayed on the web page to prove that both instances are serving traffic.

### Instance 2 — web-server-LB-2

Instance 2 was configured identically to Instance 1 so that both servers are interchangeable behind the load balancer.

- **Name:** web-server-LB-2
- **AMI / Instance type:** Ubuntu Server 26.04 LTS (HVM) · ami-01a00762f46d584a1 · t3.micro
- **Key pair:** KP-web-server-LB-1
- **VPC / Subnet:** vpc-06a5370c19a64dfce (Demo-1-vpc) · subnet-01f4692fbcaf0d945 (ap-south-1a) — 10.0.1.0/24
- **Security group:** web-server-LB-1 (reused) — SSH 22, HTTP 80, HTTPS 443 open to 0.0.0.0/0
- **Storage:** 1 x 8 GiB, gp3, root volume

> **NOTE**
> Instance 2 was later placed in Availability Zone ap-south-1b (10.0.2.0/24) once registered as a target, giving the architecture multi-AZ redundancy — confirm the subnet/AZ shown at registration in Step 2.

**User Data (Bootstrap Script)**
```bash
#!/bin/bash
apt-get update -y
apt-get install -y nginx
systemctl enable nginx
systemctl start nginx
HOSTNAME=$(hostname)
```

---

## Step 2: Target Group

A Target Group was created to register the two EC2 instances so the load balancer can route and health-check traffic to them.

**Target Group Details**
- **Target type:** Instances
- **Target group name:** TG-web-server-LB
- **Protocol:** HTTP
- **Port:** 80
- **IP address type:** IPv4
- **VPC:** vpc-06a5370c19a64dfce (Demo-1-vpc) — 10.0.0.0/16
- **Protocol version:** HTTP/1

**Health Checks**
- **Protocol:** HTTP
- **Path:** /
- **Port:** Traffic port
- **Healthy threshold:** 5
- **Unhealthy threshold:** 2
- **Timeout:** 5 seconds
- **Interval:** 30 seconds
- **Success codes:** 200
- **Target optimizer:** Off (default)

**Registered Targets**
- **Ports:** 80 for both instances
- **i-056c4ffb51456728d** — web-server-LB-2 — Port 80 — ap-south-1b — Private IPv4: 10.0.2.77 — Subnet: subnet-09d80dd29be494caf
- **i-0be47f94619a58ea6** — web-server-LB-1 — Port 80 — ap-south-1a — Private IPv4: 10.0.1.251 — Subnet: subnet-01f4692fbcaf0d945

> **NOTE**
> Registering the two instances in different Availability Zones (ap-south-1a and ap-south-1b) is what gives this architecture fault tolerance — if one AZ fails, the load balancer continues serving traffic from the other.

---

## Step 3: Application Load Balancer

An internet-facing Application Load Balancer (ALB) was created to distribute incoming HTTP traffic across the registered target group.

**Basic Configuration**
- **Type:** Application Load Balancer
- **Name:** ALB-web-server-LB
- **Scheme:** Internet-facing
- **IP address type:** IPv4

**Network Mapping**
- **VPC:** vpc-06a5370c19a64dfce (Demo-1-vpc) — 10.0.0.0/16
- **ap-south-1a (aps1-az1):** subnet-01f4692fbcaf0d945 — 10.0.1.0/24
- **ap-south-1b (aps1-az3):** subnet-09d80dd29be494caf — 10.0.2.0/24

**Security Groups**
- **Attached:** web-server-LB-1 (sg-0251639769042050f) — 3 inbound rules, 1 outbound rule

**Listeners and Routing**
- **Listener:** HTTP : 80
- **Routing action:** Forward to target group TG-web-server-LB (HTTP), weight 1 (100%)
- **Target group stickiness:** Off

**Service Integrations**

Amazon CloudFront, AWS WAF, and AWS Global Accelerator were not applied in this deployment.

**Load Balancer Details (after creation)**
- **Status:** Provisioning
- **Hosted zone:** ZP97RAFLXTNZK
- **ARN:** arn:aws:elasticloadbalancing:ap-south-1:313828097658:loadbalancer/app/ALB-web-server-LB/1ae682509f67deac
- **DNS name:** ALB-web-server-LB-121850164.ap-south-1.elb.amazonaws.com (A record)
- **Date created:** 28 July 2026, 19:17 (UTC+05:30)
- **Listeners:** HTTP:80 → Forward to TG-web-server-LB (100%), 1 rule, stickiness off

### Test Results (Browser)

The ALB DNS name was opened directly in a browser to confirm end-to-end connectivity and load-balancing behavior.

- **URL:** alb-web-server-lb-121850164.ap-south-1.elb.amazonaws.com — loaded successfully
- **Response 1:** Welcome to Nginx! Hostname: ip-10-0-2-77
- **Response 2:** Welcome to Nginx! Hostname: ip-10-0-1-251

> **NOTE**
> Alternating hostnames (ip-10-0-2-77 and ip-10-0-1-251) on repeated page refreshes confirm the Application Load Balancer is correctly round-robin distributing traffic across both web-server-LB-1 and web-server-LB-2.

---

## Step 4: Launch Template

A Launch Template was created so that the Auto Scaling Group can launch new, identically configured instances on demand.

**Launch Template Name and Description**
- **Name:** LT-web-server-LB
- **Version description:** A prod webserver for MyApp (placeholder text, not entered)
- **Auto Scaling guidance:** Not checked

**Application and OS Image (AMI)**
- **Name:** Ubuntu Server 26.04 LTS (HVM), SSD Volume Type
- **Image ID:** ami-01a00762f46d584a1
- **Username:** ubuntu
- **Architecture:** x86_64
- **Virtualization:** hvm
- **Root device type:** ebs
- **ENA enabled:** Yes
- **Boot mode:** uefi-preferred

**Instance Type & Key Pair**
- **Instance type:** t3.micro
- **Key pair name:** KP-web-server-LB-1

**Network Settings**
- **Subnet / Availability Zone:** Not included in launch template (left to the Auto Scaling group)
- **Security group:** web-server-LB-1 (sg-0251639769042050f) — VPC: vpc-06a5370c19a64dfce

**Storage**
- **Volume 1 (AMI root):** 8 GiB, EBS, gp3

**Resource Tags**
- **Tag:** Name = LT-web-server-LB (applied to Instances)

> **NOTE**
> Leaving Subnet/AZ out of the Launch Template is a deliberate best practice: it lets the Auto Scaling Group decide placement dynamically across multiple Availability Zones for high availability.

**User Data (Bootstrap Script)**
```bash
cat <<EOF > /var/www/html/index.html
<!DOCTYPE html>
<html>
<head><title>EC2 Nginx Demo</title></head>
<body style="font-family: Arial; text-align: center; margin-top: 100px;">
<h1>Welcome to Nginx!</h1>
<h2>Hostname: $HOSTNAME</h2>
</body>
</html>
EOF
# Restart Nginx
systemctl restart nginx
```

- **Base64 encoding of user data:** Not checked

---

## Step 5: Auto Scaling Group

The Auto Scaling Group (ASG) ties the Launch Template, Target Group, and Load Balancer together, automatically maintaining and scaling the fleet of web servers.

**Step 1 — Choose Launch Template**
- **ASG name:** ASG-web-server-LB
- **Launch template:** LT-web-server-LB (Version 1)
- **AMI ID:** ami-01a00762f46d584a1
- **Instance type:** t3.micro
- **Key pair:** KP-web-server-LB-1
- **Security group ID:** sg-0251639769042050f
- **Spot instances requested:** No
- **Date created:** Tue 28 Jul 2026, 19:36:36 IST (GMT+05:30)

**Step 2 — Instance Launch Options**
- **VPC:** vpc-06a5370c19a64dfce (Demo-1-vpc) — 10.0.0.0/16
- **aps1-az1 (ap-south-1a):** subnet-01f4692fbcaf0d945 — 10.0.1.0/24
- **aps1-az3 (ap-south-1b):** subnet-09d80dd29be494caf — 10.0.2.0/24
- **AZ distribution:** Balanced best effort
- **Capacity reservation preference:** Default

**Step 3 — Integrate with Other Services**
- **Load balancing:** Attach to existing target group TG-web-server-LB (ALB-web-server-LB)
- **VPC Lattice:** No VPC Lattice service
- **ARC zonal shift:** Enabled, with zonal-shift validation skipped
- **Health check behavior:** Replace unhealthy instances
- **Health checks:** EC2 (always on) + Elastic Load Balancing health checks
- **Health check grace period:** 300 seconds

**Step 4 — Group Size and Scaling**
- **Desired capacity:** 2 instances
- **Minimum capacity:** 2 instances
- **Maximum capacity:** 4 instances
- **Scaling policy:** Target tracking — Average CPU utilization
- **Target value:** 50%
- **Instance warm-up:** 300 seconds
- **Scale-in protection:** Enabled
- **CloudWatch group metrics:** Not enabled

**Steps 5 & 6 — Notifications and Tags**

No notifications and no tags were configured for this Auto Scaling Group.

> **NOTE**
> A min/max/desired of 2/4/2 with a 50% CPU target means the ASG always keeps at least 2 healthy instances behind the ALB and automatically launches up to 2 more whenever average CPU exceeds 50% — this is the core of the auto-scaling behavior requested in the objective.

---

## Final Architecture Overview

The completed Auto Scaling Group, showing both running instances healthy and attached to the target group behind the Application Load Balancer.
