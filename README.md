# FastAPI Production Deployment Project

## Overview

This project demonstrates how a FastAPI application can be deployed in a production-like environment using modern DevOps practices.

The application is containerized using Docker, orchestrated with Docker Compose, deployed on an AWS EC2 Ubuntu server, secured using NGINX as a reverse proxy, and automated using GitHub Actions CI/CD.

The goal of this project was to gain hands-on experience with real-world application deployment, infrastructure management, containerization, automation, security, and operational best practices.

---

# Live Deployment

### Application URL

http://32.192.185.7/

### Health Check Endpoint

http://32.192.185.7/health

Example Response:

```json
{
  "message": "Working"
}
```

Health Check Response:

```json
{
  "status": "healthy"
}
```

---

# Architecture

```text
Internet
    |
    v
NGINX Reverse Proxy
    |
    v
FastAPI Application Container
    |
    +------------------+
    |                  |
    v                  v
PostgreSQL         Redis
Container          Container


GitHub Repository
        |
        v
GitHub Actions CI/CD
        |
        v
AWS EC2 Ubuntu Server
```

---

# Technologies Used

## Backend

* FastAPI
* Uvicorn

## Database

* PostgreSQL 16

## Cache Layer

* Redis 7

## Containerization

* Docker
* Docker Compose

## Reverse Proxy

* NGINX

## Cloud Platform

* AWS EC2 (Ubuntu)

## CI/CD

* GitHub Actions

## Security

* UFW Firewall
* Fail2Ban

---

# Project Structure

```text
fastapi-production/
│
├── app/
│   ├── main.py
│   ├── requirements.txt
│   └── Dockerfile
│
├── docker-compose.yml
├── .env
│
├── .github/
│   └── workflows/
│       └── deploy.yml
│
└── README.md
```

---

# Application Features

The FastAPI application exposes two endpoints.

## Root Endpoint

```http
GET /
```

Response:

```json
{
  "message": "Working"
}
```

## Health Check Endpoint

```http
GET /health
```

Response:

```json
{
  "status": "healthy"
}
```

The health endpoint helps verify that the application is running correctly and can be integrated with monitoring systems.

---

# Dockerization

The application was containerized using Docker.

A Dockerfile was created to:

* Use Python 3.11 as the base image
* Install application dependencies
* Copy source code
* Run the FastAPI application using Uvicorn

### Benefits of Docker

* Consistent environments
* Easy deployment
* Portability across systems
* Simplified dependency management
* Isolation between services

---

# Docker Compose Setup

Docker Compose is used to manage multiple containers together.

The project consists of three services:

## FastAPI Container

Runs the backend application.

## PostgreSQL Container

Stores application data.

## Redis Container

Provides an in-memory data store that can be used for:

* Caching
* Session storage
* Queues
* Performance optimization

Docker Compose automatically creates a network so all containers can communicate securely.

---

# Environment Variables

Sensitive configuration values are stored using environment variables.

Example:

```env
POSTGRES_DB=appdb
POSTGRES_USER=admin
POSTGRES_PASSWORD=admin123
```

### Benefits

* Better security
* Easier environment management
* Cleaner codebase

---

# AWS EC2 Deployment

The application is deployed on an Ubuntu EC2 instance hosted on AWS.

Deployment process:

1. Launch Ubuntu EC2 Instance
2. Install Docker and Docker Compose
3. Clone the GitHub repository
4. Build Docker containers
5. Deploy services using Docker Compose
6. Configure NGINX
7. Configure firewall and security tools

### Live Application

http://32.192.185.7/

---

# NGINX Reverse Proxy

NGINX was configured as a reverse proxy.

Request Flow:

```text
User Request
      |
      v
NGINX
      |
      v
FastAPI Application
```

### Why NGINX?

* Improved security
* SSL support
* Reverse proxy functionality
* Better request handling
* Production-ready architecture

NGINX listens on port 80 and forwards requests to FastAPI running on port 8000.

---

# CI/CD Pipeline

GitHub Actions was used to automate deployment.

Workflow:

```text
Developer Pushes Code
          |
          v
GitHub Repository
          |
          v
GitHub Actions
          |
          v
SSH Connection to EC2
          |
          v
docker compose up -d --build
```

Whenever code is pushed to the main branch:

* GitHub Actions starts automatically
* Connects to EC2
* Pulls latest code
* Rebuilds containers
* Deploys updated application

### Benefits

* Faster deployments
* Reduced manual work
* Improved reliability
* Consistent deployment process

---

# Health Monitoring

The application exposes a dedicated health endpoint.

```http
GET /health
```

Response:

```json
{
  "status": "healthy"
}
```

This endpoint can be used by monitoring systems and load balancers to verify application availability.

---

# Logging Strategy

Application logs are captured using Docker.

### View Application Logs

```bash
docker logs fastapi-app
```

### View Complete Service Logs

```bash
docker compose logs
```

### Purpose

* Debugging
* Troubleshooting
* Performance monitoring
* Operational visibility

---

# Backup Strategy

Database backups are created using PostgreSQL pg_dump.

### Create Backup

```bash
docker exec fastapi-production-postgres-1 \
pg_dump -U admin appdb > backup.sql
```

### Restore Backup

```bash
psql -U admin appdb < backup.sql
```

### Benefits

* Disaster recovery
* Data protection
* Business continuity

---

# Security Measures

## UFW Firewall

Only required ports are allowed.

```text
22   SSH
80   HTTP
443  HTTPS
```

Commands used:

```bash
sudo ufw allow 22
sudo ufw allow 80
sudo ufw allow 443
sudo ufw enable
```

---

## Fail2Ban

Fail2Ban protects the server from brute-force login attempts.

Installation:

```bash
sudo apt install fail2ban -y
```

Verification:

```bash
sudo systemctl status fail2ban
```

Benefits:

* Automatic IP blocking
* Protection against SSH attacks
* Improved server security

---

# SSL Approach

A custom domain was not available during deployment.

If a domain becomes available, HTTPS can be enabled using:

* NGINX
* Let's Encrypt
* Certbot

Example:

```bash
sudo certbot --nginx
```

Benefits:

* Encrypted communication
* Secure data transfer
* Improved trust and security

---

# Future Improvements

Potential enhancements include:

* Prometheus Monitoring
* Grafana Dashboards
* Kubernetes Deployment
* Auto Scaling
* Load Balancer
* Zero Downtime Deployments

---

# What I Learned

Through this project, I gained hands-on experience with:

* FastAPI application deployment
* Docker containerization
* Docker Compose orchestration
* PostgreSQL and Redis integration
* NGINX reverse proxy configuration
* AWS EC2 server management
* GitHub Actions CI/CD automation
* Linux server administration
* Firewall and security configuration
* Logging and backup management

---

# Conclusion

This project demonstrates the complete deployment lifecycle of a backend application using modern DevOps practices.

The implementation covers:

* Containerization
* Infrastructure setup
* Reverse proxy configuration
* CI/CD automation
* Security hardening
* Logging
* Backup management
* Production deployment

The project successfully simulates a real-world deployment workflow and provides a strong foundation for deploying scalable backend applications in production environments.
