# Deploying-a-Highly-Available-Web-App-on-AWS-Using-Terraform


# 🚀 AWS Auto-Scaling Node.js App with ALB (Terraform)
This project provisions a highly available, auto-scaling Node.js application on AWS using Terraform. It deploys an Application Load Balancer (ALB), Auto Scaling Group (ASG), and EC2 instances running a simple Express app.

---
## 📌 Architecture Overview

### This infrastructure creates:
- Application Load Balancer (ALB) – Public entry point (HTTP)
- Target Group – Routes traffic to EC2 instances
- Auto Scaling Group (ASG) – Maintains and scales instances
- Launch Template – Defines EC2 configuration
- Security Groups – Controls traffic between components
- Subnets – Two Availability Zones for high availability

## 🔁 Request Flow
User → ALB (port 80) → Target Group → EC2 Instances (port 3015)
---

## 🧱 Components Breakdown

### 1. Provider Configuration
                                provider "aws" {
                                  region = var.aws_region
                                }
- Uses AWS provider
- Region is configurable via variable aws_region

### 2. Availability Zones
                                  data "aws_availability_zones" "all" {}
Fetches available AZs dynamically (not directly used, but useful for extension)

### 3. Security Groups
#### 🔐 ALB Security Group (alb_sg)
- Allows inbound HTTP (port 80) from anywhere (0.0.0.0/0)
- Allows all outbound traffic
#### 🔐 EC2 Security Group (ec2_sg)
- Allows inbound traffic only from ALB SG on port 3015
- Blocks direct public access to instances
- Allows all outbound traffic

### 4. Launch Template (EC2 Configuration)

#### Defines how EC2 instances are launched:

- AMI: var.ami_id
- Instance type: t3.micro
- SSH key: var.key_name
- Security group: ec2_sg
  
#### ⚙️ User Data Script
#### Bootstraps a Node.js app:

- Installs Node.js (LTS)
- Creates a simple Express server:
-- / → returns "Hello from ASG cluster!"
-- /health → returns 200 OK (used by ALB health checks)
- Configures a systemd service (nodeapp)
- Ensures app runs automatically on boot
