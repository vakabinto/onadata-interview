# onadata-interview
# Onadata App Deployment

## Table of Contents
- [Project Overview](#project-overview)
- [Prerequisites](#prerequisites)
- [Setup and Deployment Steps](#setup-and-deployment-steps)
  - [1. Clone the Onadata Repository](#1-clone-the-onadata-repository)
  - [2. Configure and Deploy Onadata using Docker Compose](#2-configure-and-deploy-onadata-using-docker-compose)
  - [3. Build and Push Docker Image to Docker Hub](#3-build-and-push-docker-image-to-docker-hub)
  - [4. Setup GitLab CI/CD Pipeline](#4-setup-gitlab-cicd-pipeline)
  - [5. Deploy the Onadata App on an Ubuntu Server](#5-deploy-the-onadata-app-on-an-ubuntu-server)
  - [6. Configure Nginx Web Server](#6-configure-nginx-web-server)
  - [7. Setup Server Monitoring](#7-setup-server-monitoring)
  - [8. Apply Security Configurations](#8-apply-security-configurations)
- [Resources and Links](#resources-and-links)

## Project Overview
This project involves setting up, configuring, and deploying the Onadata application as a Docker container on a Linux Ubuntu environment. It also includes Docker Hub integration, a GitLab CI/CD pipeline, and security configurations.

## Prerequisites
Before starting, ensure you have the following:
- A Linux Ubuntu server (Ubuntu 20.04 or later recommended)
- Git installed (`sudo apt install git -y`)
- Docker and Docker Compose installed (`sudo apt install docker.io docker-compose -y`)
- A Docker Hub account
- A GitLab repository to host the codebase
- Nginx installed (`sudo apt install nginx -y`)
- Monitoring tool (Prometheus + Grafana recommended)

## Setup and Deployment Steps

### 1. Clone the Onadata Repository
```sh
cd /opt
git clone https://github.com/vakabinto/onadata.git
cd onadata
```

### 2. Configure and Deploy Onadata using Docker Compose
If the repository contains a `docker-compose.yml` file, use the following command to start the application:
```sh
docker-compose up -d --build
```
To check running containers:
```sh
docker ps
```
To stop the containers:
```sh
docker-compose down
```

### 3. Build and Push Docker Image to Docker Hub
```sh
docker login -u your_dockerhub_username -p your_dockerhub_password
docker-compose build
docker tag onadata_app your_dockerhub_username/onadata:latest
docker push your_dockerhub_username/onadata:latest
```

### 4. Setup GitLab CI/CD Pipeline
Create a `.gitlab-ci.yml` file in the GitLab repository:
```yaml
stages:
  - test
  - deploy

test:
  script:
    - docker pull your_dockerhub_username/onadata:latest
    - docker run --rm your_dockerhub_username/onadata:latest pytest

deploy:
  script:
    - docker-compose down || true
    - docker pull your_dockerhub_username/onadata:latest
    - docker-compose up -d
```

### 5. Deploy the Onadata App on an Ubuntu Server
```sh
ssh user@your-server-ip
sudo docker login -u your_dockerhub_username -p your_dockerhub_password
sudo docker-compose pull
sudo docker-compose up -d
```

### 6. Configure Nginx Web Server
Create an Nginx configuration file `/etc/nginx/sites-available/onadata`:
```nginx
server {
    listen 80;
    server_name your_domain_or_ip;

    location / {
        proxy_pass http://localhost:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```
Activate the configuration and restart Nginx:
```sh
sudo ln -s /etc/nginx/sites-available/onadata /etc/nginx/sites-enabled/
sudo systemctl restart nginx
```

### 7. Setup Server Monitoring
Install Prometheus and Grafana:
```sh
sudo apt install prometheus grafana -y
```
Configure Prometheus to scrape Docker metrics and Grafana for visualization.

### 8. Apply Security Configurations
- Enable UFW firewall:
```sh
sudo ufw allow OpenSSH
sudo ufw allow 80/tcp
sudo ufw enable
```
- Use Fail2Ban to prevent brute force attacks:
```sh
sudo apt install fail2ban -y
```
- Configure SSH for key-based authentication and disable root login.

## Resources and Links
- Onadata Repository: [GitHub Link](https://github.com/vakabinto/onadata)
- Docker Hub: [Docker Hub Link](https://hub.docker.com/)
- GitLab Repository: Your GitLab repo link
- Monitoring Dashboard: Your Grafana URL

**Submission Deadline:** Friday, 28th March 2025, 4:30 PM.

