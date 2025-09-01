📋 Prerequisites
AWS account setup

Basic knowledge of AWS services

Understanding of DevSecOps principles

Familiarity with Docker, Jenkins, Java, SonarQube, AWS CLI, Kubectl, and Terraform

Docker Scout knowledge

🚀 Step-by-Step Deployment Process
Step 1: Setting up AWS EC2 Instance
Creating an EC2 instance with Ubuntu AMI

Instance type: t2.large

Storage: 30 GB

Assigning an IAM role with Admin access (for learning purposes)

Step 2: Installation of Required Tools on the Instance
Automated script installation for:

Docker

Jenkins

Java

SonarQube container

AWS CLI

Kubectl

Terraform

Step 3: Jenkins Job Configuration
Creating Jenkins jobs for:

EKS cluster creation

Hotstar clone application deployment

Jenkins Job Stages:

Send files to SonarQube for static code analysis

Run npm install

Implement OWASP for security checks

Install and run Docker Scout for container security

Scan files and Docker images with Docker Scout

Build and push Docker images

Deploy application to EKS cluster

Step 4: Clean-Up Process
Remove EKS cluster

Delete IAM role

Terminate Ubuntu instance

🔧 STEP 1A: Setting up AWS EC2 Instance and IAM Role
Sign in to AWS Management Console

Access console using your credentials

Navigate to EC2 Dashboard

Click "Services" → "Compute" → "EC2"

Launch Instance

Click "Instances" → "Launch Instance"

Choose Amazon Machine Image (AMI)

Select "AWS Marketplace"

Search for "Ubuntu" (e.g., Ubuntu Server 22.04 LTS)

Click "Select"

Choose Instance Type

Select "t2.large"

Click "Next: Configure Instance Details"

Configure Instance Details

Configure network, subnet, IAM role as needed

Click "Next: Add Storage"

Add Storage

Set root volume size to 30 GB

Click "Next: Add Tags"

Add Tags (Optional)

Add tags for better identification

Click "Next: Configure Security Group"

Configure Security Group

Create new or select existing security group

Open SSH (port 22) for remote access

Open other ports as needed

Click "Review and Launch"

Review and Launch

Review configuration details

Click "Launch"

Select or create key pair

Click "Launch Instances"

Accessing the Instance

Connect via SSH using private key

Use public IP or DNS address
