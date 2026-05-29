# Highly Available Web Application on AWS (ALB + EC2 + RDS)

This project implements a multi-tier web application architecture on AWS using an Application Load Balancer, EC2 instances deployed across multiple Availability Zones, and an RDS database layer. The infrastructure was built following a guided cloud architecture lab by Tech with Lucy and focuses on deploying and troubleshooting real AWS services.

---

## Architecture Overview
<img width="808" height="638" alt="image" src="https://github.com/user-attachments/assets/4ce45125-e826-4745-99e9-cc92458a9797" />

---

## Key AWS Services Used

- Application Load Balancer (ALB)
- EC2 Launch Templates + Auto Scaling Group
- Security Groups (network segmentation)
- Amazon RDS (MySQL)
- VPC networking (public/private subnet design)

---

## Implementation Steps

### 1. Initial Infrastructure Setup
- Created an EC2 Launch Template to define instance configuration and user data script  
- Configured a Target Group and Application Load Balancer to distribute traffic across instances  
- Deployed EC2 instances across multiple Availability Zones using an Auto Scaling Group  

---

### 2. Initial Issue: ALB Routing Failure
- Website was initially inaccessible via ALB DNS  
- Investigated Security Group configuration  
- Identified incorrect Security Group attached to ALB (default SG instead of custom `medical-app-sg`)  
- Replaced SG and restored ALB access to application  

<img width="871" height="592" alt="image" src="https://github.com/user-attachments/assets/02a0f830-0171-4754-95ca-41e85ce9676b" />

---

### 3. Second Issue: Incorrect Website Deployment
- Application loaded successfully but displayed default Apache page (“It works!”)  
- Root cause identified as incorrect file path in EC2 user data script  
- Updated Launch Template to fix deployment path to correct application directory  

<img width="619" height="142" alt="image" src="https://github.com/user-attachments/assets/92597d29-5e61-4eb8-a0d7-53334f27ae1d" />

---

### 4. Instance Refresh and Version Control
- Created new Launch Template version with corrected user data script  
- Updated Auto Scaling Group to use Launch Template version 2  
- Performed Instance Refresh to replace existing EC2 instances with updated configuration  

<img width="1651" height="806" alt="image" src="https://github.com/user-attachments/assets/d0fe5f50-488f-4d42-90e6-87fa4e784fa7" />

---

### 5. Database Security Configuration
- Configured Amazon RDS MySQL instance within a VPC  
- Created dedicated database Security Group (`rds-db-sg`)  
- Allowed inbound MySQL traffic (port 3306) only from application Security Group (`medical-sg`)  
- Ensured least-privilege network access between application and database tiers  

---

## Security Design

- ALB exposed to public internet via HTTP (port 80)  
- EC2 instances only accessible through ALB Security Group  
- RDS database isolated in private subnet and only accessible from EC2 Security Group  
- SSH access restricted to personal IP address  

---

## Key Learnings

- AWS Security Groups act as traffic filters between application tiers  
- Launch Template versions require ASG updates + instance refresh to apply changes  
- ALB routes traffic independently of instance configuration errors  
- Proper pathing in user data scripts is critical for automated deployments  
- Multi-AZ EC2 deployments improve availability even with a Single-AZ database  

---

## 🔄 Future Improvements

- Add Auto Scaling policies based on a metric type like CPU utilization  
- Move database to Multi-AZ for high availability  
- Add Route 53 domain mapping   
- Add EFS for shared storage across instances  
