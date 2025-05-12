# StreamVerse CI/CD Pipeline & Monitoring Setup

This project demonstrates the **end-to-end DevOps pipeline** for deploying and securing a Dockerized web application (StreamVerse). The setup includes Dockerization, security scans, CI/CD automation, and container monitoring.

## Table of Contents
- [Project Overview](#project-overview)
- [Prerequisites](#prerequisites)
- [Setup Instructions](#setup-instructions)
  - [1. Dockerization](#1-dockerization)
  - [2. Security Scanning](#2-security-scanning)
  - [3. CI/CD Setup](#3-cicd-setup)
  - [4. Monitoring with Prometheus and Grafana](#4-monitoring-with-prometheus-and-grafana)
- [Usage](#usage)
- [License](#license)

---

## Project Overview

This project automates the process of:
- **Dockerizing** the StreamVerse web app.
- Running **security scans** on the application using tools like **SonarQube**, **Trivy**, and **OWASP Dependency Check**.
- Setting up a **CI/CD pipeline** with **Jenkins** to automate the build, test, and deployment process.
- **Monitoring** the application with **Prometheus** and **Grafana**.

---

## Prerequisites

Before you begin, ensure that you have the following installed on your EC2 or local machine:
- **Docker**: For containerizing the application.
- **Jenkins**: For CI/CD automation.
- **SonarQube**: For static code analysis and quality gate.
- **Trivy**: For container vulnerability scanning.
- **OWASP Dependency-Check**: For dependency vulnerabilities.
- **Prometheus**: For monitoring container metrics.
- **Grafana**: For visualizing metrics from Prometheus.

---

## Setup Instructions

### 1. Dockerization

#### Set up Docker on your EC2 instance:

```bash
sudo apt-get update
sudo apt-get install docker.io -y
sudo usermod -aG docker $USER  # Replace with your system's username, e.g., 'ubuntu'
newgrp docker
sudo chmod 777 /var/run/docker.sock
