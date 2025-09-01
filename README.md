## 📋 Prerequisites
```bash
AWS account setup

Basic knowledge of AWS services

Understanding of DevSecOps principles

Familiarity with Docker, Jenkins, Java, SonarQube, AWS CLI, Kubectl, and Terraform
```

## 🚀 Step-by-Step Deployment Process

```bash
## **Step 1: Setting up AWS EC2 Instance**
- Creating an EC2 instance with Ubuntu AMI
- Instance type: t2.large
- Storage: 30 GB
- Assigning an IAM role with Admin access (for learning purposes)

## **Step 2: Installation of Required Tools on the Instance**
Automated script installation for:
- Docker
- Jenkins
- Java
- SonarQube container
- AWS CLI
- Kubectl
- Terraform

## **Step 3: Jenkins Job Configuration**
Creating Jenkins jobs for:
- EKS cluster creation
- Hotstar clone application deployment

Jenkins Job Stages:
- Send files to SonarQube for static code analysis
- Run npm install
- Implement OWASP for security checks
- Install and run Docker Scout for container security
- Scan files and Docker images with Docker Scout
- Build and push Docker images
- Deploy application to EKS cluster

## **Step 4: Clean-Up Process**
- Remove EKS cluster
- Delete IAM role
- Terminate Ubuntu instance

```
## **STEP 1A: Setting up AWS EC2 Instance and IAM Role**
```bash

### **1. Sign in to the AWS Management Console**
- Access the AWS Management Console using your credentials.

### **2. Navigate to the EC2 Dashboard**
- Click on the **“Services”** menu at the top of the page.  
- Select **“EC2”** under the **Compute** section.  
- This will take you to the EC2 Dashboard.

### **3. Launch Instance**
- Click on the **“Instances”** link on the left sidebar.  
- Click the **“Launch Instance”** button.

### **4. Choose an Amazon Machine Image (AMI)**
- In **Step 1: Choose an Amazon Machine Image (AMI)** section:  
  - Select **“AWS Marketplace”** from the left-hand sidebar.  
  - Search for **“Ubuntu”** in the search bar.  
  - Choose the desired Ubuntu AMI (e.g., **Ubuntu Server 22.04 LTS**).  
  - Click **“Select”** to proceed.

### **5. Choose an Instance Type**
- In **Step 2: Choose an Instance Type** section:  
  - Scroll through the instance types and select **t2.large**.  
  - Click **“Next: Configure Instance Details”** at the bottom.

### **6. Configure Instance Details**
- In **Step 3: Configure Instance Details** section:  
  - Leave most settings as default (or configure network, subnet, IAM role, etc., as needed).  
  - Click **“Next: Add Storage.”**

### **7. Add Storage**
- In **Step 4: Add Storage** section:  
  - Set the size of the root volume (usually `/dev/sda1`) to **30 GB**.  
  - Customize other storage settings if needed.  
  - Click **“Next: Add Tags”**.

### **8. Add Tags (Optional)**
- In **Step 5: Add Tags** section:  
  - Add tags for better identification and management (optional but recommended).  
  - Click **“Next: Configure Security Group”**.

### **9. Configure Security Group**
- In **Step 6: Configure Security Group** section:  
  - Create a new security group or select an existing one.  
  - Ensure **SSH (port 22)** is open for inbound traffic.  
  - Open other ports as needed for your application.  
  - Click **“Review and Launch”**.

### **10. Review and Launch**
- Review your instance configuration.  
- Click **“Launch”** to proceed.  
- A pop-up will prompt you to select or create a key pair.  
  - Choose an existing key pair or create a new one.  
- Click **“Launch Instances.”**

### **11. Accessing the Instance**
- Once launched, connect via **SSH** using the private key associated with the key pair.  
- Connect to the instance’s **public IP** or **DNS address**.
```
## **STEP 1B: IAM ROLE**
- Create a IAM ROLE with admin access and attached to EC2  instance that we created earlier, so we can provision cluster from that instance.
