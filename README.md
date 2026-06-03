# FastAPI Production Deployment Project

## Overview

This project demonstrates how a simple FastAPI application can be deployed in a production-like environment using Docker, Docker Compose, PostgreSQL, Redis, NGINX, GitHub Actions, and AWS EC2.

The goal of this project was not just to run a FastAPI application, but to implement the complete deployment lifecycle that is commonly used in real-world backend and DevOps environments.

The application was containerized using Docker, deployed on an Ubuntu EC2 instance, placed behind an NGINX reverse proxy, and integrated with GitHub Actions for automated deployments.

---

# Architecture

```text
Internet
    |
    v
NGINX
    |
    v
FastAPI Application
    |
    +----------------+
    |                |
    v                v
PostgreSQL        Redis


GitHub
   |
GitHub Actions
   |
EC2 Server
```

---

# Technologies Used

### Backend

* FastAPI
* Uvicorn

### Database

* PostgreSQL 16

### Cache Service

* Redis 7

### Containerization

* Docker
* Docker Compose

### Web Server

* NGINX

### Cloud Platform

* AWS EC2 (Ubuntu)

### CI/CD

* GitHub Actions

### Security

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
└── .github/
    └── workflows/
        └── deploy.yml
```

---

# Application Features

The FastAPI application provides two endpoints.

### Root Endpoint

```http
GET /
```

Response:

```json
{
  "message": "Working"
}
```

### Health Endpoint

```http
GET /health
```

Response:

```json
{
  "status": "healthy"
}
```

The health endpoint was added to quickly verify that the application is running correctly and can also be used by monitoring systems.

---

# Containerization

The application was containerized using Docker.

I created a Dockerfile that:

* Uses Python 3.11 Slim image
* Installs project dependencies
* Copies application code
* Starts FastAPI using Uvicorn

Benefits of Docker:

* Consistent environments
* Easy deployment
* Portability across systems
* Simplified dependency management

---

# Docker Compose Setup

Docker Compose was used to manage multiple services together.

The setup includes:

### FastAPI Container

Runs the backend application.

### PostgreSQL Container

Stores application data.

### Redis Container

Provides in-memory caching and can be used for sessions, queues, or performance optimization.

Docker Compose allows all services to communicate through an internal network while being managed through a single configuration file.

---

# Environment Variables

Database credentials were stored using environment variables instead of hardcoding them into the source code.

Example:

```env
POSTGRES_DB=appdb
POSTGRES_USER=admin
POSTGRES_PASSWORD=admin123
```

This approach improves security and makes configuration easier across different environments.

---

# AWS EC2 Deployment

The application was deployed on an Ubuntu EC2 instance hosted on AWS.

Deployment steps included:

1. Launch Ubuntu EC2 instance
2. Install Docker and Docker Compose
3. Clone project repository
4. Build and run containers
5. Configure NGINX reverse proxy
6. Configure security settings

The application is accessible through the EC2 public IP.

---

# NGINX Reverse Proxy

Instead of exposing FastAPI directly to the internet, NGINX was configured as a reverse proxy.

Request flow:

```text
User
  |
NGINX
  |
FastAPI
```

Benefits:

* Improved security
* Cleaner architecture
* SSL support
* Better request handling

NGINX listens on port 80 and forwards requests to the FastAPI container running on port 8000.

---

# CI/CD with GitHub Actions

To automate deployments, GitHub Actions was integrated with the project.

Workflow:

```text
Code Push
    |
GitHub Actions
    |
SSH into EC2
    |
Update Application
```

Whenever code is pushed to the main branch:

* GitHub Actions starts automatically
* Connects to EC2 through SSH
* Pulls the latest code
* Rebuilds containers
* Deploys updated application

This removes the need for manual deployments.

---

# Logging Strategy

Application logs are available through Docker.

Useful commands:

```bash
docker logs fastapi-app
```

```bash
docker compose logs
```

Logs help in troubleshooting, debugging, and monitoring application behavior.

---

# Backup Strategy

PostgreSQL backups can be created using pg_dump.

Backup command:

```bash
docker exec fastapi-production-postgres-1 \
pg_dump -U admin appdb > backup.sql
```

Restore command:

```bash
psql -U admin appdb < backup.sql
```

This ensures that database data can be recovered if needed.

---

# Security Measures

### UFW Firewall

Only required ports were allowed:

```text
22  - SSH
80  - HTTP
443 - HTTPS
```

### Fail2Ban

Fail2Ban was installed to protect the server from brute-force login attempts by automatically blocking suspicious IP addresses.

These measures provide a basic but important layer of server security.

---

# SSL Approach

A custom domain was not available during this project.

If a domain is available, HTTPS can be enabled using:

* NGINX
* Let's Encrypt
* Certbot

Example:

```bash
sudo certbot --nginx
```

This would automatically generate and manage SSL certificates.

---

# Future Improvements

Some enhancements that can be added in future versions:

* Prometheus monitoring
* Grafana dashboards
* Cloudflare integration
* Kubernetes deployment
* Auto Scaling
* Zero-downtime deployments
* Blue-Green deployment strategy

---

# Conclusion

This project demonstrates the complete deployment lifecycle of a backend application using modern DevOps practices. It covers containerization, infrastructure setup, reverse proxy configuration, CI/CD automation, security, logging, and backup management.

Through this project, I gained practical experience in deploying and managing applications in a production-like environment using Docker, NGINX, GitHub Actions, and AWS EC2.
