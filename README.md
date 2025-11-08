# aws-alb-project
AWS ALB project: VPC, 2x Ubuntu EC2 (Apache), Target Group, ALB

# 🚀 AWS Application Load Balancer Project (EC2 + Apache + VPC)

This project demonstrates how to deploy a **highly available web application** on AWS using **EC2 instances (Ubuntu + Apache)** behind an **Application Load Balancer (ALB)**.  
All resources were created via the AWS Management Console (no CLI), perfect for beginners learning core AWS networking and load balancing concepts.

---

## 🧩 Architecture Overview

**Components:**
- **VPC:** Custom VPC with CIDR `172.31.0.0/16`
- **Subnets:** Two public subnets in separate Availability Zones
- **Internet Gateway + Route Tables:** For outbound internet access
- **EC2 Instances:** Two Ubuntu servers (t2.micro)
- **Apache Web Server:** Installed on each EC2 instance
- **Security Group:** Allows HTTP (80) and SSH (22)
- **Application Load Balancer (ALB):** Distributes HTTP traffic across both EC2 instances
- **Target Group:** Contains both EC2 instances, with health checks enabled

---

## 🏗️ Step-by-Step Implementation

### 1️⃣ Create the VPC
- Navigate to **VPC → Create VPC**
- CIDR Block: `172.31.0.0/16`
- Enable DNS hostnames and DNS resolution

### 2️⃣ Create Two Public Subnets
- **Subnet-1:** `172.31.1.0/24` in `us-east-1a`
- **Subnet-2:** `172.31.2.0/24` in `us-east-1b`
- Enable **Auto-assign public IP**

### 3️⃣ Internet Gateway and Routing
- Create and attach an **Internet Gateway** to the VPC
- Update **Route Table** to add:

- Associate both subnets with this route table

### 4️⃣ Launch EC2 Instances
- AMI: **Ubuntu Server 24.04 LTS**
- Instance type: **t2.micro**
- Network: select your custom VPC and subnets
- Security group:
- Inbound: HTTP (80) and SSH (22)
- Outbound: All traffic
- User Data (to install Apache):
```bash
#!/bin/bash
sudo apt update -y
sudo apt install apache2 -y
sudo systemctl enable apache2
sudo systemctl start apache2
echo "Hello from web-server-1 (Ubuntu)" | sudo tee /var/www/html/index.html

- Repeat for the second instance, changing the message to:
echo "Hello from web-server-2 (Ubuntu)" | sudo tee /var/www/html/index.html



### 5️⃣ Create Target Group

- EC2 → Target Groups → Create Target Group
- Type: Instances
- Protocol: HTTP (port 80)
- Register both EC2 instances
- Health check path: /

### 6️⃣ Create Application Load Balancer

- EC2 → Load Balancers → Create → Application Load Balancer
- Scheme: Internet-facing
- IP type: IPv4
- Network mapping: select both subnets
- Security group: allow HTTP (80)
- Listener: HTTP port 80 → forward to your target group
- Wait for the ALB status to become Active.

### 7️⃣ Test the Load Balancer

- Copy the ALB DNS name, e.g.: my-alb-1234567890.us-east-1.elb.amazonaws.com
- Open it in a browser:  http://my-alb-1234567890.us-east-1.elb.amazonaws.com
- Refresh multiple times — you should see responses alternate:
Hello from web-server-1
Hello from web-server-2

### 🎯 Load balancing confirmed!
