# IT Vedant DevOps Assignment

## Project Overview

This project demonstrates a practical end-to-end DevOps implementation for a Java-based web application using AWS, Jenkins, Docker, Amazon ECR, Kubernetes, Amazon S3, CloudFront, Application Load Balancer, and Auto Scaling Group.

The assignment focuses on implementing automated application delivery, containerization, Kubernetes deployment, secure static website delivery, load balancing, auto scaling, and automatic replacement of unhealthy EC2 instances.

---

## Objectives

The main objectives of this assignment are:

* Implement a CI/CD pipeline using Jenkins
* Run application tests automatically
* Build and package the Java application
* Containerize the application using Docker
* Push Docker images to Amazon ECR
* Deploy the application to Kubernetes
* Host a static website using Amazon S3
* Deliver the static website through Amazon CloudFront
* Configure CloudFront cache invalidation
* Configure an Application Load Balancer
* Configure an Auto Scaling Group
* Configure CPU-based scaling at 70%
* Demonstrate automatic replacement of unhealthy instances

---

# 1. CI/CD Pipeline

A Jenkins CI/CD pipeline was implemented to automate the application build, Docker image creation, ECR push, and Kubernetes deployment process.

### Pipeline Stages

The Jenkins pipeline performs the following steps:

1. **Test** – Runs Maven tests.
2. **Build Application** – Packages the Java application using Maven.
3. **Docker Build** – Builds the Docker image.
4. **ECR Login and Push** – Authenticates with Amazon ECR and pushes the Docker image.
5. **Deploy to Kubernetes** – Deploys the application using Kubernetes manifests.
6. **Rollout Verification** – Verifies that the Kubernetes deployment completes successfully.

### Commands Used

Application testing:

```bash
mvn clean test
```

Application build:

```bash
mvn package -DskipTests
```

Docker image build:

```bash
docker build -t itvedant-devops-app:<BUILD_NUMBER> .
```

### Result

The Jenkins pipeline successfully completed all stages and deployed the application to Kubernetes.

**Evidence:**
`screenshots/jenkins-pipeline-success.png`

---

# 2. Docker Containerization

The Java application was containerized using Docker.

A Docker image was created as part of the Jenkins pipeline and tagged using the Jenkins build number.

The image was then pushed to a private Amazon ECR repository.

### Containerization Flow

```text
Java Application
      ↓
Docker Build
      ↓
Docker Image
      ↓
Amazon ECR
```

**Evidence:**
`screenshots/ecr-image-tag.png`

---

# 3. Kubernetes Deployment

The application was deployed to Kubernetes using Kubernetes Deployment and Service manifests.

### Kubernetes Configuration

* Deployment name: `itvedant-app`
* Replicas: 2
* Service type: NodePort
* Application port: 8080
* Readiness probe configured
* ECR image pull secret configured

The Kubernetes configuration files are available in the `kubernetes/` directory.

### Kubernetes Files

```text
kubernetes/
├── deployment.yaml
└── service.yaml
```

The deployment was successfully verified with two running application pods.

**Evidence:**

* `screenshots/kubernetes-pods-running.png`
* `screenshots/cicd-kubernetes-application.png`

---

# 4. Amazon S3 and CloudFront

A static website was hosted using Amazon S3 and delivered through Amazon CloudFront.

## S3 Configuration

The S3 bucket was configured as a private bucket with public access blocked.

The website contains an `index.html` file with the assignment information.

## CloudFront Configuration

CloudFront was configured with:

* Amazon S3 as the origin
* Origin Access Control (OAC)
* Private S3 bucket access
* Default root object: `index.html`
* CloudFront distribution
* Cache invalidation

The website was successfully accessed through the CloudFront distribution.

### Request Flow

```text
User
 ↓
CloudFront
 ↓
Private S3 Bucket
 ↓
index.html
```

**Evidence:**

* `screenshots/cloudfront-static-website.png`
* `screenshots/cloudfront-distribution.png`

---

# 5. CloudFront Cache Invalidation

CloudFront cache invalidation was configured and tested using:

```text
/*
```

This allows updated content to be served without waiting for existing cached objects to expire.

**Evidence:**
`screenshots/cloudfront-distribution.png`

---

# 6. Application Load Balancer

An internet-facing Application Load Balancer (ALB) was configured to distribute HTTP traffic across EC2 instances.

A Target Group was created and associated with the ALB.

### Target Group Configuration

* Protocol: HTTP
* Port: 80
* Health check path: `/`
* Healthy instances receive application traffic
* Unhealthy instances are removed from service

The application was successfully accessed through the ALB DNS name.

**Evidence:**
`screenshots/alb-application-running.png`

---

# 7. Auto Scaling Group

An EC2 Auto Scaling Group was configured to maintain application availability.

### Configuration

| Setting          | Value           |
| ---------------- | --------------- |
| Minimum Capacity | 2               |
| Desired Capacity | 2               |
| Maximum Capacity | 4               |
| Scaling Policy   | Target Tracking |
| CPU Target       | 70%             |
| Health Check     | EC2 + ELB       |

EC2 instances are launched using a Launch Template.

The Auto Scaling Group maintains the required number of application instances and works together with the Application Load Balancer.

---

# 8. CPU-Based Auto Scaling

A Target Tracking scaling policy was configured with an average CPU utilization target of **70%**.

When the workload increases and CPU utilization goes above the configured target, the Auto Scaling Group can launch additional instances.

When demand decreases, the Auto Scaling Group can scale in according to the configured policy.

**Evidence:**
`screenshots/cpu-scaling-70-percent.png`

---

# 9. Automatic Instance Replacement

Automatic replacement of an unhealthy EC2 instance was demonstrated using the Auto Scaling Group.

During the test, an EC2 instance was terminated, and the Auto Scaling Group automatically launched a replacement instance to maintain the required capacity.

### Result

* Unhealthy/terminated instance detected
* ASG initiated replacement
* New EC2 instance launched
* Desired capacity maintained

**Evidence:**
`screenshots/asg-instance-replacement.png`

---

# 10. Application Availability

The application was tested through the configured infrastructure and successfully served traffic through the Application Load Balancer.

The Auto Scaling Group maintains multiple instances, while the Target Group performs health checks to ensure traffic is directed to healthy instances.

---

# 11. Repository Structure

```text
itvedant-devops-assignment/
│
├── README.md
├── Jenkinsfile
├── Dockerfile
│
├── app/
│   ├── pom.xml
│   └── src/
│       └── main/
│
├── kubernetes/
│   ├── deployment.yaml
│   └── service.yaml
│
└── screenshots/
    ├── jenkins-pipeline-success.png
    ├── ecr-image-tag.png
    ├── kubernetes-pods-running.png
    ├── cicd-kubernetes-application.png
    ├── cloudfront-static-website.png
    ├── cloudfront-distribution.png
    ├── cpu-scaling-70-percent.png
    ├── asg-instance-replacement.png
    └── alb-application-running.png
```

---

# 12. Technologies Used

| Category                | Technology                |
| ----------------------- | ------------------------- |
| Source Control          | Git, GitHub               |
| CI/CD                   | Jenkins                   |
| Build Tool              | Maven                     |
| Application             | Java / Spring Boot        |
| Containerization        | Docker                    |
| Container Registry      | Amazon ECR                |
| Container Orchestration | Kubernetes                |
| Cloud Platform          | AWS                       |
| Object Storage          | Amazon S3                 |
| CDN                     | Amazon CloudFront         |
| Load Balancing          | Application Load Balancer |
| Auto Scaling            | EC2 Auto Scaling Group    |

---

# 13. Key DevOps Practices Demonstrated

* CI/CD automation
* Automated application testing
* Maven build automation
* Docker containerization
* Private container image storage
* Kubernetes application deployment
* Kubernetes readiness checks
* Secure S3 and CloudFront configuration
* Load balancing
* CPU-based auto scaling
* EC2 health checks
* Automatic instance replacement
* High availability through multiple instances

---

# Conclusion

This assignment demonstrates a practical DevOps workflow covering application build and testing, containerization, continuous delivery, Kubernetes deployment, secure static content delivery, load balancing, auto scaling, and automatic infrastructure recovery.

The implementation and results are supported by the screenshots available in the `screenshots/` directory.
