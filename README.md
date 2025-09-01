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

## **Step 2: Installation of Required Tools on the Instance**
- Scripts to install Required tools
- sudo su    #Into root
- vi script1.sh
```bash
#!/bin/bash
sudo apt update -y
wget -O - https://packages.adoptium.net/artifactory/api/gpg/key/public | tee /etc/apt/keyrings/adoptium.asc
echo "deb [signed-by=/etc/apt/keyrings/adoptium.asc] https://packages.adoptium.net/artifactory/deb $(awk -F= '/^VERSION_CODENAME/{print$2}' /etc/os-release) main" | tee /etc/apt/sources.list.d/adoptium.list
sudo apt update -y
sudo apt install temurin-17-jdk -y
/usr/bin/java --version
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update -y
sudo apt-get install jenkins -y
sudo systemctl start jenkins
#install docker
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg -y
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
sudo usermod -aG docker ubuntu
newgrp docker
```
- sudo chmod 777 script1.sh
- sh script1.sh

**Script 2 for Terraform,kubectl,Aws cli**
- vi script2.sh

```bash
#!/bin/bash
#install terraform
sudo apt install wget -y
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform
#install Kubectl on Jenkins
sudo apt update
sudo apt install curl -y
curl -LO https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
kubectl version --client
#install Aws cli
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
sudo apt-get install unzip -y
unzip awscliv2.zip
sudo ./aws/install
```
- sudo chmod 777 script2.sh
- sh script2.sh
  
- Now Run sonarqube container
- sudo chmod 777 /var/run/docker.sock
- docker run -d --name sonar -p 9000:9000 sonarqube:lts-community
- Ec2 is created in the Aws console
- Now copy the public IP address of ec2 and paste it into the browser
- - Ec2-ip:8080
  - - Connect your Instance to Putty or Mobaxtreme and provide the below command for the Administrator password
- sudo cat /var/lib/jenkins/secrets/initialAdminPassword
- And complete Jekins Configuration.
- Now Copy the public IP again and paste it into a new tab in the browser with 9000
- <img width="1106" height="526" alt="image" src="https://github.com/user-attachments/assets/2c21e8e2-9a00-4b0a-a3b0-150d81fd3454" />
- username admin
- password admin

## Step 3: Jenkins Job Configuration
** EKS Provision job **
```bash
That is done now go to Jenkins and add a terraform plugin to provision the AWS EKS using the Pipeline Job.
Go to Jenkins dashboard –> Manage Jenkins –> Plugins

Available Plugins, Search for Terraform and install it.
Go to Putty and use the below command

let’s find the path to our Terraform (we will use it in the tools section of Terraform)
- which terraform
Now come back to Manage Jenkins –> Tools
Add the terraform in Tools
```
<img width="1156" height="591" alt="image" src="https://github.com/user-attachments/assets/7773bdfd-2055-40bb-a6ea-198ba63a5dfa" />
- Apply and save.
- CHANGE YOUR S3 BUCKET NAME IN THE BACKEND.TF in repo

