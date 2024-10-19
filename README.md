# AWS WAF with Load Balancer

## Overview
This project demonstrates how to set up an AWS infrastructure to host a web application with the following key components:
- **EC2 Instance**: Launched in a private subnet within a VPC, hosting a basic Nginx web server.
- **Application Load Balancer (ALB)**: Launched in public subnets, forwarding web traffic to the EC2 instance in the private subnet.
- **AWS WAF**: Protecting the Load Balancer, allowing the inspection of incoming traffic, and setting up custom rules to block, allow, or count specific requests.

## AWS WAF Overview
**AWS Web Application Firewall (WAF)** is a cloud-native firewall that lets you control how your application responds to different types of web requests. It protects AWS resources such as CloudFront, API Gateway, Load Balancers, and AppSync by inspecting web requests for user-defined rules. AWS WAF helps block attacks like SQL injection, Cross-site Scripting (XSS), and bot-driven abuse while providing fine-grained controls such as rate limiting, IP filtering, and CAPTCHA challenges.

## Steps

### 1. Create an EC2 Instance in a Private Subnet

1. **Launch EC2**: 
   - Create an EC2 instance within a **private subnet** of your VPC.
   - Ensure the EC2 instance is not directly accessible from the internet since it will sit behind a load balancer.

2. **User Data Configuration**:
   When launching the EC2 instance, attach the following user data script. This script will:
   - Install Nginx.
   - Serve a simple "Hello World" page.

```bash
#!/bin/bash
# Update the package repository
sudo apt update -y

# Install Nginx
sudo apt install nginx -y

# Start Nginx service
sudo systemctl start nginx

# Enable Nginx to start on boot
sudo systemctl enable nginx

# Create a simple "Hello World" HTML file
echo "<h1>Hello World from Nginx on EC2</h1>" | sudo tee /var/www/html/index.html

# Restart Nginx to apply changes
sudo systemctl restart nginx
```

3. **Private Subnet**: Make sure that the EC2 instance is in a private subnet with no direct internet access.

### 2. Set Up an Application Load Balancer (ALB)

1. **Create a Load Balancer**:
   - Navigate to the **Load Balancers** section in the AWS Console.
   - Select **Application Load Balancer**.
   - Choose at least **two public subnets** to ensure redundancy. One of these public subnets should be in the same availability zone as the EC2 instance’s private subnet.
   - Configure a **listener on port 80** for HTTP traffic.

2. **Target Group Configuration**:
   - Create a target group that points to the EC2 instance launched in the private subnet.
   - Ensure health checks are configured properly (e.g., check the Nginx default port 80).

3. **Test Load Balancer**:
   - Once the load balancer is created, copy the DNS name provided by the load balancer.
   - Open the DNS URL in your browser. You should see the "Hello World" page being served from the EC2 instance behind the load balancer.

### 3. Configure AWS WAF for the Load Balancer

1. **Create a WAF Web ACL**:
   - Go to **AWS WAF** in the AWS Console.
   - Create a new **Web ACL** and associate it with the Application Load Balancer.
   - Set the default action for the Web ACL (allow or block requests).

2. **Set Up IP Sets**:
   - Create an **IP Set** to define specific IP addresses for which you want to apply rules.
   - You can define trusted IPs that will be allowed, suspicious IPs to be blocked, or IPs whose requests will be counted.

3. **Add Custom Rules**:
   - Add rules to the WAF Web ACL based on the IP sets.
   - For example, create rules to:
     - **Allow**: Allow requests from specific IP addresses.
     - **Block**: Block requests from specific IP addresses.
     - **Count**: Count requests from certain IP addresses to monitor without affecting traffic.
   
   You can also define additional conditions like blocking traffic based on **geographic locations**, **rate limiting** (e.g., block if too many requests come from one IP), or **query string** inspection.

### 4. Test the Setup

1. **Access the Load Balancer**:
   - Open the DNS URL of the load balancer in a browser from a client machine.
   - Based on the rules defined in AWS WAF, requests from certain IPs will either be **allowed**, **blocked**, or **counted**.

2. **Monitoring and Logs**:
   - AWS WAF provides logging capabilities. You can monitor WAF logs to review how your rules are applied and ensure your web application is secure.

## Conclusion
With this setup, you've created a secure environment where an EC2 instance hosts a web server in a private subnet, accessed through a load balancer in public subnets. AWS WAF provides an additional layer of security by controlling and monitoring traffic to your load balancer, allowing fine-grained management of web requests.

This setup is useful for improving security, ensuring scalability, and providing redundancy for your web application. You can further customize your WAF rules to adapt to specific security needs and traffic patterns.
