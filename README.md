
# Automated CI/CD Pipeline using Jenkins, Docker & AWS EC2

## 1. Project Overview

This project demonstrates an automated Continuous Integration
and Continuous Deployment (CI/CD) pipeline.

A web application is containerized using Docker and deployed
through Jenkins on an AWS EC2 instance.

The objective is to automate application build, testing,
and deployment whenever new code is pushed to GitHub.

## 2. Technologies Used

- Git and GitHub
- Jenkins
- Docker
- AWS EC2
- Linux (Amazon Linux / Ubuntu)
- HTML and CSS
- Shell scripting

## 3. Project Architecture

Developer
   |
   v
GitHub Repository
   |
   v
Jenkins CI/CD Pipeline
   |
   v
Docker Image Build
   |
   v
Docker Container
   |
   v
AWS EC2 Deployment
   |
   v
Live Web Application

## 4. Project Structure

devops-cicd-project/
|
|-- index.html
|-- Dockerfile
|-- Jenkinsfile
|-- .dockerignore
|-- README.md

## 5. Current Implementation

- Created a sample web application.
- Created a Dockerfile.
- Built Docker image: devops-cicd-app:v1
- Ran Docker container: devops-app
- Configured port mapping: 8081:80
- Verified application using localhost:8081

## 6. Future Implementation

- Push source code to GitHub.
- Configure Jenkins.
- Create automated CI/CD pipeline.
- Deploy application on AWS EC2.
- Test automatic deployment after code changes.

## 7. Project Status

Docker-based local deployment completed.
Jenkins and AWS EC2 integration are pending.
