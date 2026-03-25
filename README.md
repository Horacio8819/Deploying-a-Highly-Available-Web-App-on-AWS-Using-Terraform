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
- / → returns "Hello from ASG cluster!"
- /health → returns 200 OK (used by ALB health checks)
- Configures a systemd service (nodeapp)
- Ensures app runs automatically on boot

### 5. Application Load Balancer (ALB)
                                              resource "aws_lb" "app_alb"
- Internet-facing (internal = false)
- Attached to 2 subnets (multi-AZ) and uses alb_sg

### 6. Target Group
                                              resource "aws_lb_target_group" "app_tg"
- Port: 3015
- Protocol: HTTP
- Attached to default VPC
- ❤️ Health Check
- Path: /health
- Interval: 30s
- Timeout: 5s
- Healthy threshold: 2
 -Unhealthy threshold: 2

### 7. ALB Listener
                                              resource "aws_lb_listener" "http"
- Listens on port 80
- Forwards traffic to target group

### 8. Auto Scaling Group (ASG)
                                            resource "aws_autoscaling_group" "app_asg"
- Desired capacity: 2 instances
- Min: 2
- Max: 5
- Uses launch template
- Attached to target group
##### ⚡ Health Checks
- Type: ELB
- Grace period: 60 seconds

### 9. Networking
#### Default VPC
                                                data "aws_vpc" "default"
Uses AWS default VPC
#### Subnets
                                                subnet_a → AZ a → 172.31.100.0/24  
                                                subnet_b → AZ b → 172.31.101.0/24
### ▶️ How to Deploy
- terraform init
- terraform plan
- terraform apply

### 🧪 Testing

#### Root endpoint:

http://<ALB_DNS>/ 

                                                  → Hello from ASG cluster!

#### Health check:

http://<ALB_DNS>/health

                                                                  → OK

