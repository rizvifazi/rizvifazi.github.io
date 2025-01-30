layout: post
title: "AWS 3 Tier Architecture"
excerpt: "In this post I have demonstrated how we can manually implement a well architected, 3 tier deployment in AWS, later I have also discussed how we can reproduce the same using a terraform"
categories: [AWS, Well Architected, Secured Deployment, Terraform]
comments: true
image:
  feature: 3-tier-project.png
  credit: rizvifazi
  creditlink: https://youtu.be/UxbH_Fj5p0g
---

# AWS 3 Tier Architecture

### **Architecture Flow**  
1. User → **Internet-Facing ALB** → **Public-ASG** (Web Tier) → **Private-ASG** (App Tier) → **RDS** (DB Tier).  
2. **S3** and **Lambda** integrated for storage and serverless workflows.  
3. **SNS** and **CloudWatch** for alerts and monitoring.  

Demo video:
<iframe width="560" height="315" src="https://www.youtube.com/embed/UxbH_Fj5p0g?si=6JTzJMiMAz025goX" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>



## Step-by-Step Guide to AWS 3-Tier Architecture Implementation  

---
#### 1. Project Setup & VPC Configuration  
- **Created a VPC** (Virtual Private Cloud) to isolate resources.  
- Designed subnets:  
  - Public subnets (for web tier).  
  - Private subnets (for application tier and database tier).  
- Configured route tables, internet gateways, and NAT gateways for connectivity.


---

#### 2. Security Groups Configuration  
- **Public-SG**:  
  - Allowed inbound traffic from `0.0.0.0/0` (internet) for HTTP/HTTPS access.  
- **Private-SG**:  
  - Restricted inbound traffic to only instances associated with **Public-SG**.  
- **RDS-SG**:  
  - Allowed access exclusively from **Private-SG** for database security.  

---

#### 3. RDS Subnet Group Setup  
- Created a **DB subnet group** for Amazon RDS.  
- Assigned private subnets to the subnet group to host the database tier (e.g., MySQL/PostgreSQL).  

---

#### 4. Auto Scaling Groups (ASG)  
- **Public-ASG**:  
  - Launched EC2 instances in public subnets (web tier).  
  - Configured scaling policies based on CPU/memory usage.  
- **Private-ASG**:  
  - Launched EC2 instances in private subnets (application tier).  
  - Ensured instances are only accessible via **Public-SG**.  
---

#### 5. Load Balancers & Target Groups  
- **Internet-Facing Application Load Balancer (ALB)**:  
  - Deployed in public subnets.  
  - Forwarded traffic to **Public-ASG** instances via target groups.  
- **Internal Load Balancer (optional)**:  
  - Used for communication between application tier (private) and database tier.  

---

#### 6. Web Application Deployment  
- Hosted a website on **Public-ASG** instances.  
- Validated load balancing by refreshing instances and confirming traffic distribution.  

---

#### 7. Accessing Private Resources  
- **Bastion Host**:  
  - Used a public EC2 instance (in **Public-SG**) to SSH into private instances (**Private-SG**).  
- **RDS Connection**:  
  - Accessed the database from the application tier (private instances).  
- **S3 Integration**:  
  - Uploaded `live_demo.txt` to an S3 bucket from the public server.  

---

#### 8. S3, RDS, and SNS Setup  
- **S3 Bucket**:  
  - Created for static assets or file storage.  
- **RDS Database**:  
  - Configured a relational database (e.g., MySQL) in the private subnet group.  
- **SNS Notifications**:  
  - Sent alerts (e.g., scaling events) to a registered email via Amazon SNS.  

---

#### 9. Serverless Components  
- **Lambda Function**:  
  - Deployed for event-driven tasks (e.g., processing S3 uploads).  
- **SQS Queue**:  
  - Used to decouple components (e.g., sending messages between services).  

---

#### 10. Monitoring & Logging  
- **CloudWatch Logs**:  
  - Tracked metrics and logs for EC2, RDS, Lambda, and ASG.  


---


### Terraform Implementation

[Highly Available AWS 3 Tier Terraform Template](https://github.com/rizvifazi/aws-ha-tf)

#### 1. Configure the AWS CLI  
```shell
$ aws configure
AWS Access Key ID [****************MRAE]: 
AWS Secret Access Key [****************6wNJ]: 
```

#### 2. Install and Validate Terraform

Install Terraform using your system's package manager or download it from the [official website](https://www.terraform.io/downloads).

**Linux (Ubuntu/Debian):**
```shell
sudo apt-get update && sudo apt-get install -y gnupg software-properties-common
wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform
```

**Validate Installation:**

```shell
terraform -version
```

This should display the installed Terraform version (e.g., `Terraform v1.5.7`), confirming a successful installation.


#### 3. Clone the Repo and cd

```shell
git clone https://github.com/rizvifazi/aws-ha-tf.git
```


#### 4. Execute terraform commands  

```HCL
# Intialises provider plugin and modules  
terraform init  
# Validates the configuration files  
terraform validate  
# Shows the execution plan  
terraform plan  
# Creates the infrastructure  
terraform apply
```
