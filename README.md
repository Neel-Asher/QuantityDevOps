# CI/CD Pipeline for Spring Boot Application using AWS, Jenkins, Docker, and ECR

## Project Overview

This project demonstrates a complete end-to-end CI/CD pipeline for a Spring Boot application using a multi-server AWS architecture. The pipeline automates build, containerization, image storage, and deployment using Jenkins, Docker, and Amazon Elastic Container Registry (ECR).

The system is designed with separation of concerns across multiple EC2 instances:

- Jenkins Server for CI/CD orchestration
- Database Server for MySQL
- Docker/Deployment Server for application runtime (optional extension)
- Backend Server for Spring Boot orchastration

---

## Architecture

The pipeline follows this workflow:

1. Developer pushes code to GitHub repository
2. Jenkins server detects changes and triggers pipeline
3. Maven builds the Spring Boot application
4. Docker image is created
5. Docker image is pushed to AWS ECR
6. Deployment server pulls image from ECR
7. Container is run and application becomes accessible

---

## Infrastructure Setup

### 1. Jenkins Server (CI/CD Server)

Ubuntu 24.04 EC2 instance configured with:

- Jenkins
- Java 21 (OpenJDK)
- Maven
- Docker
- AWS CLI
- IAM credentials configured for ECR access

Responsibilities:
- Executes Jenkins pipelines
- Builds application using Maven
- Creates Docker images
- Pushes images to ECR
- Deploys to target EC2 instances

---

### 2. Database Server (MySQL EC2 Instance)

Ubuntu 24.04 EC2 instance configured with:

- MySQL 8.x
- Remote access enabled for application connectivity
- Dedicated database `quantitydb`
- User `quantityadmin` configured for remote access

Responsibilities:
- Stores application data
- Isolated from application and CI/CD layer

---

### 3. AWS Elastic Container Registry (ECR)

Amazon ECR repository used for storing Docker images.

Repository:
- quantity-app

Features:
- Image versioning using tags
- Docker push/pull integration
- IAM-based authentication using AWS CLI

---

### 4. Docker Deployment Environment

Docker installed on EC2 instance for application runtime.

Responsibilities:
- Pull images from ECR
- Run Spring Boot containers
- Expose application on port 8080

---

## CI/CD Pipeline Flow

### Jenkins Pipeline Stages

1. Git Checkout  
   Clones repository from GitHub

2. Build  
   Maven clean install to generate JAR file

3. Docker Build  
   Builds Docker image using Dockerfile

4. Docker Tag  
   Tags image for AWS ECR repository

5. AWS ECR Login  
   Authenticates Docker with AWS ECR

6. Docker Push  
   Pushes image to ECR repository

7. Deployment (Optional)  
   SSH into deployment server and run container:
   - Pull latest image
   - Stop existing container
   - Remove old container
   - Run new container

---

## Tools and Technologies

- Java 21 (Spring Boot)
- Maven
- Docker
- Jenkins
- AWS EC2
- AWS ECR
- AWS CLI
- Ubuntu 24.04 LTS
- MySQL 8

---

## Project Structure

QuantityMeasurementSpringBoot/
├── src/
├── pom.xml
├── Dockerfile
├── Jenkinsfile
└── README.md

---

## Docker Commands

### Build Image

docker build -t quantity-app .

### Tag Image for ECR

docker tag quantity-app:latest <aws_account_id>.dkr.ecr.<region>.amazonaws.com/quantity-app:latest

### Login to ECR

aws ecr get-login-password --region <region> | docker login --username AWS --password-stdin <aws_account_id>.dkr.ecr.<region>.amazonaws.com

### Push Image

docker push <aws_account_id>.dkr.ecr.<region>.amazonaws.com/quantity-app:latest

---

## AWS Configuration

### IAM Permissions Required

Jenkins EC2 instance requires:

- AmazonEC2ContainerRegistryFullAccess
- AmazonEC2FullAccess (optional for deployments)
- CloudWatchLogsAccess (optional monitoring)

---

## Database Configuration

Host: <DB_PRIVATE_IP>  
Port: 3306  
Database: quantitydb  
Username: quantityadmin  

---

## Key Learnings

- Multi-server AWS architecture design
- Jenkins CI/CD pipeline automation
- Docker image lifecycle management
- AWS ECR integration with Docker
- IAM-based authentication for AWS services
- Separation of CI/CD, database, and runtime layers

---

## Future Improvements

- Kubernetes deployment using EKS
- Blue-green deployment strategy
- Prometheus and Grafana monitoring
- Automated rollback in Jenkins pipeline
- Secure Jenkins using Nginx reverse proxy and HTTPS

---

## Author

DevOps project demonstrating real-world CI/CD pipeline using AWS, Jenkins, Docker, and Spring Boot.