AWS WAF with Load Balancer
Overview
This project demonstrates the setup of an AWS environment with the following components:

An EC2 instance in a private subnet of a VPC running a basic Nginx web server.
An Application Load Balancer (ALB) in public subnets, routing traffic to the private EC2 instance.
AWS WAF (Web Application Firewall) protecting the load balancer by controlling access based on custom rules.
Steps
1. Create an EC2 Instance in a Private Subnet
Launch an EC2 instance in a private subnet under a VPC. Use the following user data script to configure an Nginx web server that serves a simple "Hello World" page:

bash
Copy code
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

# Restart Nginx to load the new page
sudo systemctl restart nginx
2. Create an Application Load Balancer
Set up a load balancer in at least two public subnets. One of these subnets must be in the same availability zone as the private EC2 instance.
In the listener configuration, allow traffic on port 80.
Create a target group pointing to the private EC2 instance.
Once the load balancer is launched, copy the DNS name and access it via your browser. You should see the "Hello World" page.

3. Configure AWS WAF for the Load Balancer
Create an AWS WAF and associate it with the load balancer as the protected resource.
Set up IP sets to define which IP addresses the WAF rules will apply to.
Create custom rules (e.g., block, allow, or count requests) based on the IP addresses in the IP set.
4. Testing the Setup
Hit the load balancer's DNS URL from the IP addresses defined in your IP set.
Depending on the WAF rules, requests will either be blocked, allowed, or counted.