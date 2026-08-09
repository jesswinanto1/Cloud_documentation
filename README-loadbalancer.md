# Hosting a Website on Two Servers with a Load Balancer

AWS Application Load Balancer — Lab Documentation

**By:** Jesswin Anto 

## Objective

To host a website on two separate EC2 servers and use an Application Load Balancer (ALB) to distribute incoming requests between them, switching servers for every request.

---

## Step 1: Create a VPC with Two Public Subnets in Different Zones

A VPC was created with two public subnets placed in different Availability Zones to support high availability across the load balancer.


---

## Step 2: Launch Two EC2 Instances

Both instances were launched using the following bootstrap script to install and configure Nginx automatically:

### Bash Script Used on Both Instances

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
# Create a simple web page
cat <<EOF > /var/www/html/index.html
<!DOCTYPE html>
<html>
<head>
<title>EC2 Nginx Demo</title>
</head>
<body style="font-family: Arial; text-align: center; margin-top: 100px;">
<h1>Welcome to Nginx!</h1>
<h2>Hostname: $HOSTNAME</h2>
</body>
</html>
EOF
# Restart Nginx
systemctl restart nginx
```

**Note:** The script uses the instance hostname ($HOSTNAME) inside the web page so each server returns a different response. This makes it easy to visually confirm the load balancer is switching between servers.

### 1) LB-web-server-1



### 2) Verify Server 1

Open the IPv4 address in a new browser tab: **13.206.235.8**



### 3) Verify Server 2

Open and launch LB-web-server-2, then open its IPv4 address in a new tab: **13.232.70.146**



---

## Step 3: Preparing the Target Group

Here are all the selected values from the Create Target Group workflow:

### Target Group Details
- **Target type:** Instances
- **Target group name:** LB-web-server-TG
- **Protocol:** HTTP
- **Port:** 80
- **IP address type:** IPv4
- **VPC:** vpc-06a5370c19a64dfce (Demo-1-vpc) — 10.0.0.0/16
- **Protocol version:** HTTP1

### Health Checks
- **Health check protocol:** HTTP
- **Health check path:** /
- **Health check port:** traffic-port
- **Interval:** 30 seconds
- **Timeout:** 5 seconds
- **Healthy threshold:** 5
- **Unhealthy threshold:** 2
- **Success codes:** 200

**Note:** A lower healthy/unhealthy threshold makes the load balancer react faster to server failures, but can cause a healthy server to be marked unhealthy due to a single slow response. The default values here (5/2) balance stability and responsiveness.

### Target Optimizer
- Off - Default

### Register Targets
- **Ports for selected instances:** 80

Targets (2):
- i-05b69ca94435be88d — LB-web-server-2 — Port 80 — ap-south-1b
- i-0ea0bbd318b008a23 — LB-web-server-1 — Port 80 — ap-south-1a

### Create Target Group


---

## Step 4: Creating the Load Balancer

Here are all the selected values from the Create Application Load Balancer page:

### Basic Configuration
- **Load balancer name:** Web-Server-LB
- **Scheme:** Internet-facing
- **Load balancer IP address type:** IPv4

### Network Mapping
- **VPC:** vpc-06a5370c19a64dfce (Demo-1-vpc) — 10.0.0.0/16

Availability Zones and subnets:
- ap-south-1a (aps1-az1): subnet-01f4692fbcaf0d945 (Demo-1-subnet-public1-ap-south-1a) — 10.0.1.0/24
- ap-south-1b (aps1-az3): subnet-09d80dd29be494caf (Demo-1-subnet-public2-ap-south-1b) — 10.0.2.0/24

### Security Groups
- LB-web-server-SG (sg-072525d6ec0930abb) — Inbound rule (3), Outbound rule (1)

### Listeners and Routing (Default)
- **Listener:** HTTP:80
- **Protocol:** HTTP
- **Port:** 80
- **Routing action:** Forward to target groups
- **Target group:** TG-web-server-LB (HTTP) — Weight: 1, Percent: 100%
- **Target group stickiness:** Off

**Note:** Target group stickiness, if enabled, routes a client's repeated requests to the same server for a set duration. It is kept Off here so requests are distributed across both servers, which better demonstrates load balancing behavior.


