# Deploying-a-Highly-Available-Web-App-on-AWS-Using-Terraform


# 🚀 AWS Auto-Scaling Node.js App with ALB (Terraform)
This project provisions a highly available, auto-scaling Node.js application on AWS using Terraform. It deploys an Application Load Balancer (ALB), Auto Scaling Group (ASG), and EC2 instances running a simple Express app.


# 📌 Architecture Overview

This infrastructure creates:

- Application Load Balancer (ALB) – Public entry point (HTTP)
- Target Group – Routes traffic to EC2 instances
- Auto Scaling Group (ASG) – Maintains and scales instances
- Launch Template – Defines EC2 configuration
- Security Groups – Controls traffic between components
- Subnets – Two Availability Zones for high availability
