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
- CHANGE YOUR S3 BUCKET NAME IN THE BACKEND.TF in repo.
  
### Now create a new job for the Eks provision

  <img width="1079" height="497" alt="image" src="https://github.com/user-attachments/assets/cbcedec2-5cdd-49f0-a666-a90c1706db76" />
  
#### I want to do this with build parameters to apply and destroy while building only.

### you have to add this inside job like the below image

<img width="1097" height="637" alt="image" src="https://github.com/user-attachments/assets/126171f2-0da8-4f53-9158-2f8f647457bc" />

```bash
Let’s add a pipeline
pipeline{
    agent any
    stages {
        stage('Checkout from Git'){
            steps{
                git branch: 'main', url: 'https://github.com/Aj7Ay/Hotstar-Clone.git'
            }
        }
        stage('Terraform version'){
             steps{
                 sh 'terraform --version'
             }
        }
        stage('Terraform init'){
             steps{
                 dir('EKS_TERRAFORM') {
                      sh 'terraform init'
                   }
             }
        }
        stage('Terraform validate'){
             steps{
                 dir('EKS_TERRAFORM') {
                      sh 'terraform validate'
                   }
             }
        }
        stage('Terraform plan'){
             steps{
                 dir('EKS_TERRAFORM') {
                      sh 'terraform plan'
                   }
             }
        }
        stage('Terraform apply/destroy'){
             steps{
                 dir('EKS_TERRAFORM') {
                      sh 'terraform ${action} --auto-approve'
                   }
             }
        }
    }
}
```

- #### let’s apply and save and Build with parameters and select action as apply
- <img width="1086" height="269" alt="image" src="https://github.com/user-attachments/assets/8581baaa-57ce-4853-9551-c154e1575dd3" />
- #### Stage view it will take max 10mins to provision
<img width="1081" height="415" alt="image" src="https://github.com/user-attachments/assets/ae8575d2-a75e-4442-a87c-e3bd383f1298" />
Check in Your Aws console whether it created EKS or not.

# **Step 3B: Hotstar job**
### Plugins installation & setup (Java, Sonar, Nodejs, owasp, Docker)

- Go to Jenkins dashboard
- Manage Jenkins –> Plugins –> Available Plugins
- Search for the Below Plugins
- Eclipse Temurin installer
- Sonarqube Scanner
- NodeJs
- Owasp Dependency-Check
- Docker
- Docker Commons
- Docker Pipeline
- Docker API
- Docker-build-step

### Configure in Global Tool Configuration
Goto Manage Jenkins → Tools → Install JDK(17) and NodeJs(16)→ Click on Apply and Save
<img width="1121" height="498" alt="image" src="https://github.com/user-attachments/assets/c108929e-5f3d-4159-a394-dfe566376b8b" />
<img width="1097" height="494" alt="image" src="https://github.com/user-attachments/assets/50ad2b73-8eb9-4a4d-9506-58f55ab494f8" />
### For Sonarqube use the latest version
<img width="1102" height="578" alt="image" src="https://github.com/user-attachments/assets/2acd2f31-2c40-4a5f-9c33-dc04558d53ad" />

### Use the latest version of Docker
<img width="1147" height="571" alt="image" src="https://github.com/user-attachments/assets/9e643909-877b-4098-934a-d19d2828e7da" />

### Configure Sonar Server in Manage Jenkins
- Grab the Public IP Address of your EC2 Instance
- Sonarqube works on Port 9000, so <Public IP>:9000.
- Goto your Sonarqube Server.
- Click on Administration → Security → Users → Click on Tokens and Update Token → Give it a name → and click on Generate Token

- <img width="1266" height="644" alt="image" src="https://github.com/user-attachments/assets/d70db9e3-0e44-44a3-943e-f41c1da60619" />

### Create a token with a name and generate
<img width="1173" height="452" alt="image" src="https://github.com/user-attachments/assets/94873142-b0ce-452c-8b73-5f563b39605d" />

- Goto Jenkins Dashboard → Manage Jenkins → Credentials → Add Secret Text. It should look like this
  
- <img width="1185" height="557" alt="image" src="https://github.com/user-attachments/assets/23a46d98-95b7-464e-b589-96f8a2e14f39" />
- You will this page once you click on create
- Now, go to Dashboard → Manage Jenkins → System and Add like the below image.
- <img width="1214" height="625" alt="image" src="https://github.com/user-attachments/assets/84f2e1e9-882b-4e0b-a8e7-d608e4d6bae2" />
- Click on Apply and Save

- In the Sonarqube Dashboard add a quality gate also

- Administration–> Configuration–>Webhooks
- <img width="1240" height="569" alt="image" src="https://github.com/user-attachments/assets/aac5a9eb-1f5c-408d-b46e-c3f8f6be204b" />
- Click on Create
- <img width="1210" height="321" alt="image" src="https://github.com/user-attachments/assets/e4cef8bc-1d88-4b53-a401-33ce5b2a51d3" />
- Add details
- http://jenkins-public-ip:8080&gt;/sonarqube-webhook/
- <img width="1116" height="574" alt="image" src="https://github.com/user-attachments/assets/cdff697f-3fb2-4a0c-9211-022fc064048e" />
- Now add Docker credentials to the Jenkins to log in and push the image

- Manage Jenkins –> Credentials –> global –> add credential

- Add DockerHub Username and Password under Global Credentials
- <img width="1139" height="628" alt="image" src="https://github.com/user-attachments/assets/420633bd-6633-4a1a-ac48-d9b95929ad33" />
### Pipeline upto Docker
- Now let’s create a new job for our pipeline
- <img width="1070" height="494" alt="Screenshot 2025-09-01 130902" src="https://github.com/user-attachments/assets/8e67a467-a847-411b-bddf-c1eb69f29ba1" />
- Before Adding pipeline install Docker Scout
```bash
docker login    #use credentials to login
curl -sSfL https://raw.githubusercontent.com/docker/scout-cli/main/install.sh | sh -s -- -b /usr/local/bin
```
- Add this to Pipeline
```bash
pipeline{
    agent any
    tools{
        jdk 'jdk17'
        nodejs 'node16'
    }
    environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }
    stages {
        stage('clean workspace'){
            steps{
                cleanWs()
            }
        }
        stage('Checkout from Git'){
            steps{
                git branch: 'main', url: 'https://github.com/PrajwalBambulkar/Hotstar-Devops.git'
            }
        }
        stage("Sonarqube Analysis "){
            steps{
                withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Hotstar \
                    -Dsonar.projectKey=Hotstar'''
                }
            }
        }
        stage("quality gate"){
           steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'Sonar-token'
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }
        stage('OWASP FS SCAN') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'DP-Check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage('Docker Scout FS') {
            steps {
                script{
                   withDockerRegistry(credentialsId: 'docker', toolName: 'docker'){
                       sh 'docker-scout quickview fs://.'
                       sh 'docker-scout cves fs://.'
                   }
                }
            }
        }
        stage("Docker Build &amp; Push"){
            steps{
                script{
                   withDockerRegistry(credentialsId: 'docker', toolName: 'docker'){
                       sh "docker build -t hotstar ."
                       sh "docker tag hotstar prajwalbambulkar/hotstar:latest "
                       sh "docker push prajwalbambulkar/hotstar:latest"
                    }
                }
            }
        }
        stage('Docker Scout Image') {
            steps {
                script{
                   withDockerRegistry(credentialsId: 'docker', toolName: 'docker'){
                       sh 'docker-scout quickview prajwalbambulkar/hotstar:latest'
                       sh 'docker-scout cves prajwalbambulkar/hotstar:latest'
                       sh 'docker-scout recommendations prajwalbambulkar/hotstar:latest'
                   }
                }
            }
        }
        stage("deploy_docker"){
            steps{
                sh "docker run -d --name hotstar -p 3000:3000 prajwalbambulkar/hotstar:latest"
            }
        }
    }
}
```


- Click on Apply and save.
- - Build now
- To see the report, you can go to Sonarqube Server and go to Projects.
- <img width="1110" height="172" alt="Screenshot 2025-09-01 131936" src="https://github.com/user-attachments/assets/b71544c2-302b-42d4-90d9-f053988a8c8b" />
- When you log in to Dockerhub, you will see a new image is created

#### Deploy to Container  
- access ec2-ip:3000 you will see output
- <img width="1114" height="486" alt="image" src="https://github.com/user-attachments/assets/c55e437a-8192-4ca4-af7f-9e5acbfb5955" />
- Go to Putty of your Jenkins instance SSH and enter the below command
  - aws eks update-kubeconfig --name CLUSTER NAME --region CLUSTER REGION
  - aws eks update-kubeconfig --name EKS_CLOUD --region ap-south-1
  - kubectl get nodes
  - Now Give this command in CLI
      - cat /root/.kube/config
      - Copy the config file to Jenkins master or the local file manager and save it.

<img width="1106" height="627" alt="image" src="https://github.com/user-attachments/assets/0246b7cc-1986-4309-ad57-469f22564013" />

### copy it and save it in documents or another folder save it as secret-file.txt.
### Note: create a secret-file.txt in your file explorer save the config in it and use this at the kubernetes credential section.

  #### Install Kubernetes Plugin, Once it’s installed successfully
  <img width="1139" height="521" alt="image" src="https://github.com/user-attachments/assets/e85415b3-5dfb-4b80-81ca-8cd687004693" />
  - goto manage Jenkins –> manage credentials –> Click on Jenkins global –> add credentials
  - <img width="1139" height="553" alt="image" src="https://github.com/user-attachments/assets/f35bc1e9-630d-4a2c-a159-28e26f889f12" />
  
## final step to deploy on the Kubernetes cluster.
```bash


  stage('Deploy to kubernets'){
            steps{
                script{
                    dir('K8S') {
                        withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'k8s', namespace: '', restrictKubeConfigAccess: false, serverUrl: '') {
                                sh 'kubectl apply -f deployment.yml'
                                sh 'kubectl apply -f service.yml'
                        }
                    }
                }
            }
        }
```

- Give the command after pipeline success
- kubectl get all

- ### Add Load balancer IP address to cluster ec2 instance security group and copy load balancer Link and open in a browser
- <img width="1110" height="467" alt="image" src="https://github.com/user-attachments/assets/dd90b47e-88bb-4bd3-a03e-aacc03e65133" />

## Step 4: Destruction
- Now Go to Jenkins Dashboard and click on Terraform-Eks job.

- And build with parameters and destroy action.

- It will delete the EKS cluster that provisioned.
- 
<img width="1080" height="340" alt="image" src="https://github.com/user-attachments/assets/e4eeb807-8099-4adb-a6c0-74da772cba09" />
After 10 minutes cluster will delete and wait for it. Don’t remove ec2 instance till that time.












