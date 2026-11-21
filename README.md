# Automated CI/CD Pipeline using Jenkins, Docker, GitHub & AWS EC2

## 1. Project Overview

This project demonstrates an automated Continuous Integration and Continuous Deployment (CI/CD) pipeline using Jenkins, Docker, GitHub, Docker Hub, and AWS EC2.

Whenever a developer pushes new code to the GitHub repository, a GitHub webhook automatically triggers Jenkins.

Jenkins builds and tests the Docker image, pushes it to Docker Hub, and deploys the application to an AWS EC2 instance through SSH.

The deployment process includes automated health checks to verify application availability.

**Project Status:** Successfully implemented and tested (Jenkins Build #17).

---

## 2. Technologies Used

| Technology | Purpose |
|---|---|
| Git | Version control |
| GitHub | Source code repository |
| GitHub Webhook | Automatic Jenkins trigger |
| Jenkins | CI/CD automation |
| Docker | Application containerization |
| Docker Hub | Docker image registry |
| AWS EC2 | Cloud deployment |
| Ubuntu Linux | EC2 operating system |
| NGINX | Web server |
| Shell Scripting | Deployment automation |
| HTML/CSS | Sample web application |
| SSH | Secure remote deployment |

---

## 3. Project Architecture

```text
       Developer
           |
           v
     Git Push (GitHub)
           |
           v
    GitHub Webhook
           |
           v
     Jenkins Server
           |
           v
    Checkout Source Code
           |
           v
    Build Docker Image
           |
           v
     Test Docker Image
           |
           v
    Push to Docker Hub
           |
           v
    Local Deployment
           |
           v
    Local Health Check
           |
           v
      SSH Connection
           |
           v
       AWS EC2
           |
           v
   Pull Docker Image
           |
           v
   Replace Old Container
           |
           v
    Run New Container
           |
           v
     EC2 Health Check
           |
           v
     Live Web Application
```

---

## 4. Project Structure

```text
devops-cicd-project/
│
├── index.html
├── Dockerfile
├── Jenkinsfile
├── .dockerignore
└── README.md
```

### File Description

- `index.html` – Sample web application.
- `Dockerfile` – Instructions to build the Docker image.
- `Jenkinsfile` – Defines the automated CI/CD pipeline.
- `.dockerignore` – Excludes unnecessary files from the Docker build context.
- `README.md` – Project documentation.

---

## 5. AWS EC2 Configuration

The application is deployed on an AWS EC2 instance.

| Configuration | Value |
|---|---|
| Cloud Provider | AWS |
| Service | EC2 |
| Region | Mumbai (ap-south-1) |
| Instance Type | t3.micro |
| Operating System | Ubuntu Linux |
| Architecture | x86_64 |
| Application Port | 80 |
| Deployment Method | SSH |

### Security Group Configuration

| Type | Port | Purpose |
|---|---:|---|
| SSH | 22 | Remote server access (restricted to My IP) |
| HTTP | 80 | Public web application access |

Docker was installed and configured on the EC2 instance.

---

## 6. Docker Configuration

The application is containerized using Docker and NGINX.

### Build Docker Image

```bash
docker build -t devops-cicd-app:v1 .
```

### Run Docker Container

```bash
docker run -d -p 8081:80 --name devops-app devops-cicd-app:v1
```

### Verify Running Containers

```bash
docker ps
```

The Jenkins pipeline builds versioned images using the Jenkins build number.

Example:

```text
woharami/devops-cicd-project:17
woharami/devops-cicd-project:latest
```

Docker images are built for the `linux/amd64` platform to match the AWS EC2 architecture.

---

## 7. Jenkins Configuration

Jenkins is used to automate the complete deployment workflow.

### Jenkins Job

```text
devops-cicd-pipeline
```

### Required Credentials

Two Jenkins credentials are configured:

1. `dockerhub-credentials` – Docker Hub username and access token.
2. `ec2-ssh-key` – SSH private key credential for EC2 access.

**Security:** Credentials and private keys are stored in Jenkins Credentials. They are not hardcoded in the Jenkinsfile or repository.

### GitHub Webhook

A GitHub webhook is configured to trigger Jenkins automatically when new code is pushed.

Webhook endpoint format:

```text
https://YOUR-JENKINS-ENDPOINT/github-webhook/
```

The Jenkins job uses the GitHub hook trigger for GITScm polling option.

A running ngrok tunnel is used to expose the local Jenkins server for webhook delivery.

---

## 8. CI/CD Pipeline Stages

The Jenkinsfile contains the following stages:

### Stage 1: Checkout

Retrieves source code from the GitHub repository.

### Stage 2: Build Docker Image

Builds the Docker image for the application using the AMD64 platform.

### Stage 3: Test Docker Image

Checks whether the Docker image was created successfully.

### Stage 4: Push to Docker Hub

Authenticates using Jenkins credentials and pushes versioned and latest image tags.

### Stage 5: Deploy Container

Runs the Docker container locally on the Jenkins host using port 8082.

### Stage 6: Local Health Check

Checks application availability using:

```bash
curl -fsS http://localhost:8082/
```

### Stage 7: Deploy to AWS EC2

Jenkins connects to EC2 through SSH.

The deployment process:

- Pulls the latest build-number image from Docker Hub.
- Removes the previous application container.
- Starts the new container.
- Configures automatic container restart.
- Runs an HTTP health check.

EC2 health check:

```bash
curl -fsS http://127.0.0.1/
```

If the health check succeeds, Jenkins marks the pipeline as successful.

---

## 9. Application Deployment Verification

### Check Docker Containers on EC2

```bash
sudo docker ps
```

### Check Application Response

```bash
curl -I http://localhost
```

### Access the Public Application

```text
http://YOUR-EC2-PUBLIC-IP
```

The application is accessible through the EC2 public IP on port 80.

### Verified Deployment

Jenkins Build #17 completed successfully.

Verified Docker image:

```text
woharami/devops-cicd-project:17
```

Verified container:

```text
devops-cicd-app
```

Container status:

```text
Up and running
```

---

## 10. Troubleshooting

### Docker Daemon Not Running

Start Docker Desktop on macOS and verify:

```bash
docker info
```

### SSH Permission Issues

Verify the SSH key permissions:

```bash
chmod 400 ~/Downloads/devops-cicd-key.pem
```

### GitHub Webhook Not Triggering Jenkins

- Verify that the ngrok tunnel is running.
- Check the GitHub webhook delivery status.
- Verify the webhook URL.
- Check Jenkins GitHub trigger configuration.

### EC2 Deployment Issues

Verify SSH connectivity and EC2 security group rules.

```bash
ssh -i ~/Downloads/devops-cicd-key.pem ubuntu@YOUR-EC2-PUBLIC-IP
```

### Docker Container Status

```bash
sudo docker ps -a
```

### Container Logs

```bash
sudo docker logs devops-cicd-app
```

### AMD64 and ARM64 Platform Warning

The Jenkins host uses Apple Silicon (ARM64), while the EC2 instance uses x86_64 architecture.

The pipeline builds images using:

```bash
docker build --platform linux/amd64
```

This ensures compatibility with the EC2 deployment environment.

---

## 11. Skills Demonstrated

- CI/CD pipeline implementation
- Jenkins Pipeline and Groovy basics
- Git and GitHub workflow
- GitHub webhook integration
- Docker image creation and container management
- Docker Hub image publishing
- AWS EC2 provisioning and deployment
- Linux administration
- SSH-based remote deployment
- Shell scripting
- Application health checks
- Troubleshooting and deployment verification

---

## 12. Resume Project Description

**Automated CI/CD Pipeline using Jenkins, Docker, GitHub & AWS EC2**

- Developed an automated CI/CD pipeline using Jenkins and GitHub webhooks.
- Containerized a sample web application using Docker and published versioned images to Docker Hub.
- Automated application deployment to AWS EC2 through SSH-based integration.
- Implemented local and remote HTTP health checks to verify deployment success.
- Configured Docker container restart policies for application availability.

---

## 13. Future Improvements

Potential improvements for the next version:

- Implement automated rollback.
- Add deployment notifications.
- Integrate monitoring using Prometheus and Grafana.
- Add automated application testing.
- Implement Ansible for server configuration management.
- Explore Kubernetes-based deployment.

---

## 14. Important Notes

- This project uses a single EC2 instance.
- The deployment replaces the previous application container.
- Automated rollback and blue-green deployment are not currently implemented.
- AWS resources may incur charges. Stop the EC2 instance when it is not required and review associated storage and public IPv4 costs.

---

## 15. Project Status

**Successfully Completed and Verified**

- GitHub webhook integration: Working
- Jenkins pipeline: Successful
- Docker image build: Successful
- Docker Hub push: Successful
- AWS EC2 deployment: Successful
- Local health check: Successful
- EC2 health check: Successful
- Public application access: Verified

**Last verified Jenkins build: #17**
