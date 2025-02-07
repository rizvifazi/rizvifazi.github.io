---
layout: post
title: "Spring Boot App CI/CD using Jenkins"
excerpt: "In this post I have demonstrated how I implement an end-to-end CI/CD pipeline using GitHub, Jenkins, Maven and Ansible to deploy a Spring Boot webapp to Kubernetes"
categories: [CICD, Jenkins, Ansible, Kubernetes, Azure Kubernetes Service, Spring Boot, Java]
comments: true
image:
  feature: /ci_cd/ci_cd_pipeline_with_jenkins.png
  credit: rizvifazi
  creditlink: https://rizvifazi.github.io/
---



# Spring Boot App CI/CD using Jenkins

---

## **Architecture Flow**  
1. **CI** :  User → **Commit code to GitHub**(SCM) → **Jenkins Build Artifacts using Maven Plugin**(Build)  → **Artifacts Deployed to Ansible Server**
2. **CD** :  **Ansible Server Builds Docker Image** → **Push Image to Docker Hub**  → **Deploy app to Azure Kubernetes Service**

---

### Tools Used:

**GitHub**: For Source Code Management and Versioning
**Jenkins**: For orchestrating the CI/CD Pipeline
**Maven**: To build Artifacts
**Docker**: Containerize the Application using Dockerfile
**Ansible**: To automate deployment using ansible-playbooks
**DockerHub**: Versioning Docker images, centralized image repository.
**Azure Kubernetes Service**: Container orchestration

---

## Demo Video

Demo video:

### Will be published on 08.02.2025

---

## **Step-by-Step Implementation**

### **1. Setup GitHub Repository**

- Create a repository on GitHub and push the Spring Boot application code.
- Ensure it contains a `Dockerfile` and `Jenkinsfile`.

### **2. Install and Configure Jenkins**

#### **Install Jenkins on a Server**

```
sudo apt update
sudo apt install openjdk-11-jdk -y
wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt install jenkins -y
sudo systemctl start jenkins
sudo systemctl enable jenkins
```

#### **Install Required Plugins**

- **GitHub Plugin**
- **Publish Over SSH**
- **Maven Integration**

### **3. Configure Jenkins Pipeline (CI)**

#### **Jenkinsfile**

```
pipeline {
    agent any
    environment {
        DOCKER_CREDENTIALS_ID = 'docker-hub-credentials'
    }
    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/your-repo.git'
            }
        }
        stage('Build with Maven') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Archive Artifact') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }
        stage('Deploy to Ansible Server') {
            steps {
                sh 'scp target/*.jar ansible@your-ansible-server:/opt/artifacts/'
            }
        }
    }
}
```

### **4. Configure Ansible for Deployment (CD)**

#### **Install Ansible on the Deployment Server**

```
sudo apt update
sudo apt install ansible -y
```

#### **Ansible Playbook:** `**deploy.yml**`

```
- name: Deploy Dockerized Spring Boot App
  hosts: all
  become: true
  tasks:
    - name: Build Docker Image
      command: docker build -t your-dockerhub-username/app:latest .

    - name: Push Docker Image to Docker Hub
      command: docker push your-dockerhub-username/app:latest

    - name: Deploy to Kubernetes
      command: kubectl apply -f deployment.yaml
```

### **5. Create Kubernetes Deployment Configuration**

#### `**deployment.yaml**`

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: springboot-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: springboot-app
  template:
    metadata:
      labels:
        app: springboot-app
    spec:
      containers:
        - name: springboot-app
          image: your-dockerhub-username/app:latest
          ports:
            - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: springboot-service
spec:
  type: LoadBalancer
  ports:
    - port: 80
      targetPort: 8080
  selector:
    app: springboot-app
```

### **6. Configure Azure Kubernetes Service (AKS)**

#### **Install Azure CLI and Set Up AKS**

```
az login
az group create --name myResourceGroup --location eastus
az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 2 --enable-addons monitoring --kubernetes-version 1.29.2 --generate-ssh-keys
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

#### **Deploy Application to AKS**

```
kubectl apply -f deployment.yaml
kubectl get services
```

### **7. Automate CI/CD with Jenkins**

- Add a webhook in GitHub to trigger the Jenkins build on code commit.
    
- Configure Jenkins to trigger Ansible playbooks after successful builds.
    

### **8. Verify Deployment**

- Check running pods: `kubectl get pods`
- Get service external IP: `kubectl get services`
- Access the application using the external IP in the browser.

