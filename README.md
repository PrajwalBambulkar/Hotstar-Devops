Deploying HOTSTAR using CI / CD DevSecOps
========================================


Step 1: Setting up AWS EC2 Instance
----------------------------------
Creating an EC2 instance t2.large with Ubuntu 24 AMI, t2.large, and 30 GB storage
Assigning an IAM role with Admin access

Step 2: Installation of Required Tools on the Instance
------------------------------------------------------

Docker
Jenkins
Java
SonarQube container
AWS CLI
Kubectl
Terraform

Step 3: Jenkins Job Configuration
-----------------------------------
Creating Jenkins jobs for:
========================
Job 1: Creating an EKS cluster
Job 2: Deploying the Hotstar clone application

Configuring the Jenkins job stages:
-----------------------------------
Sending files to SonarQube for static code analysis
Running npm install
Implementing OWASP for security checks
Installing and running Docker Scout for container security
Scanning files and Docker images with Docker Scout
Building and pushing Docker images
Deploying the application to the EKS cluster
