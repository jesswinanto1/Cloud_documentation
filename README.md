# Cloud_documentation
A centralized repository dedicated exclusively to cloud technical documentation, including cloud architecture designs, service configurations, deployment procedures, and infrastructure management guides.
# AWS Cloud Lab Documentation — Study Order Index

This index arranges the lab reports in a logical learning order — from basic connectivity, through networking fundamentals, to storage, and finally full application architecture.

| # | Topic | File | Why this order |
|---|---|---|---|
| 1 | SSH Connection Basics | [SSH_Connection_Lab_Report_Jesswin.md](./README-ssh-connection-lab.md) | Start here — connecting to a remote server is the most basic skill needed before doing anything else on AWS EC2. |
| 2 | VPC Subnetting & CIDR Theory | [AWS_VPC_Subnetting_CIDR_Task.md](./README-vpc-subnetting-cidr.md) | Learn the CIDR/subnetting math and AWS-specific IP reservation rules before building a VPC — theory before practice. |
| 3 | VPC Manual Creation | [VPC_Manual_Creation_Report.md](./README-vpc-manual-creation.md) | Apply the subnetting theory hands-on: manually create a VPC, subnets, route tables, IGW, and a VPC endpoint step by step. |
| 4 | VPC Automatic Creation | [vpc_creation_automatic.md](./README-vpc-creation-automatic.md) | Once you understand the manual process, see the faster "VPC and more" wizard that automates the same setup. |
| 5 | S3 Bucket Basics | [S3_bucket_task.md](./README-s3-bucket-task.md) | Move to storage: creating buckets, folders, uploading files, permissions, and cross-bucket copy. |
| 6 | S3 Versioning & CloudFront | [AWS_S3_Versioning_CloudFront_Lab_Report.md](./README-s3-versioning-cloudfront.md) | Build on S3 basics with versioning and static website hosting delivered through a CloudFront CDN. |
| 7 | Load Balancer Basics | [LoadBalancer.md](./README-loadbalancer.md) | Shift to compute/networking: distribute traffic across two EC2 servers with an Application Load Balancer. |
| 8 | Auto Scaling Group (Full Architecture) | [Auto-scaling-group_Documentation.md](./README-autoscaling-group.md) | Capstone — combines Launch Template, Target Group, ALB, and Auto Scaling Group into one complete, self-healing architecture. |

---

### Suggested path
```
SSH Basics → CIDR/Subnetting Theory → VPC (Manual) → VPC (Automatic)
   → S3 Basics → S3 Versioning + CloudFront → Load Balancer → Auto Scaling Group
```
