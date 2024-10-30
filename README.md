# AWS Auto Scaling with EC2, Load Balancer, and Target Groups

This guide walks you through setting up horizontal scaling using AWS Auto Scaling Groups (ASGs), EC2 instances, a Load Balancer, Target Groups, and related AWS resources.

## Prerequisites
- AWS account with IAM permissions to create EC2, ASG, Load Balancer, ACM, and associated resources.
- Basic understanding of EC2, ASGs, and load balancing concepts.

## Glossary
- **AMI (Amazon Machine Image)**: Snapshot of a machine from which new EC2 instances are created.
- **Load Balancer**: Entry point to your application, routing requests to available machines in the Target Group.
- **Target Group**: A group of EC2 instances that a Load Balancer can route requests to.
- **Launch Template**: Template defining machine specifications (AMI, instance type, etc.) for creating new instances.
- **Auto Scaling Groups (ASG)**: Automatically scales instances based on defined metrics.

> 💡 **Reminder**: Clean up resources after use to avoid unnecessary charges.

---

## Setup Guide

### 1. Create an EC2 Instance
1. Launch a new EC2 instance.
2. Install Node.js on the instance:
    ```bash
    # Follow the DigitalOcean guide for Ubuntu 20.04: https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-20-04
    ```
3. Clone the repository:
    ```bash
    git clone https://github.com/100xdevs-cohort-2/week-22
    ```
4. Create an AMI (Amazon Machine Image) from your configured instance.

### 2. Configure User Data Script
The following user data script will configure and start the application on boot:
```bash
#!/bin/bash 
export PATH=$PATH:/home/ubuntu/.nvm/versions/node/v22.0.0/bin/
echo "hi there before"
echo "hi there after"
npm install -g pm2
cd /home/ubuntu/week-22
pm2 start index.js
pm2 save
pm2 startup
Note: Verify if user data is working by checking logs. Refer to StackOverflow guide.

3. Set Up Auto Scaling Group (ASG)
Create a Security Group with necessary inbound and outbound rules.
Create a Launch Template using your AMI.
Define an ASG and attach it to your Launch Template. Select multiple Availability Zones for high availability.
Configure the desired minimum and maximum instance limits in ASG to test scaling.
4. Configure Load Balancer and Target Group
Set up a Load Balancer with HTTPS Listener and request an SSL certificate from ACM.
Create a Target Group and attach it to your ASG.
Attach your Load Balancer to the ASG.
Testing Auto Scaling
1. Test ASG Scaling Policies
Dynamic Scaling Policy: Configure a scaling policy to manage instance count based on metrics like CPU usage.
2. Simulate Load and Observe Scaling
To simulate a load increase, SSH into an instance and run:

javascript
Copy code
let c = 0;
while (1) {
    c++;
}
Scale Up: The ASG should add instances as the load increases.
Scale Down: Terminate the loop to observe automatic instance termination as load decreases.
3. Failover Testing
Stop a few instances in the ASG; new instances should launch to meet the desired capacity.
Cleanup Guide
To avoid incurring charges, delete the following resources in order:

ASG
Target Group
Load Balancer
Launch Template
AMI
Instance from which AMI was created
