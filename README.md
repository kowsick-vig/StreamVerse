# 🚀 StreamVerse CI/CD Pipeline & Monitoring Setup

This project demonstrates the end-to-end DevOps pipeline for deploying and securing a Dockerized web application (StreamVerse). The setup includes Dockerization, security scans, CI/CD automation, and container monitoring.

## 📋 Project Description

This project automates the process of:
- **Dockerizing** the StreamVerse web app.
- Running **security scans** on the application using tools like **SonarQube**, **Trivy**, and **OWASP Dependency Check**.
- Setting up a **CI/CD pipeline** with **Jenkins** to automate the build, test, and deployment process.
- Monitoring the application with **Prometheus** and **Grafana**.

---

## 🛠️ Prerequisites

Before you begin, ensure that you have the following installed on your EC2 or local machine:

- **Docker**: For containerizing the application.
- **Jenkins**: For CI/CD automation.
- **SonarQube**: For static code analysis and quality gate.
- **Trivy**: For container vulnerability scanning.
- **OWASP Dependency-Check**: For dependency vulnerabilities.
- **Prometheus**: For monitoring container metrics.
- **Grafana**: For visualizing metrics from Prometheus.

---

## 🚀 Setup Instructions

### 1. Dockerization
Set up Docker on your EC2 instance:
```bash
sudo apt-get update
sudo apt-get install docker.io -y
sudo usermod -aG docker $USER  # Replace with your system's username, e.g., 'ubuntu'
newgrp docker
sudo chmod 777 /var/run/docker.sock
````

Build and run the application container:

```bash
docker build -t streamverse .
docker run -d --name streamverse -p 8081:80 streamverse:latest
```

To stop and remove the container:

```bash
docker stop <container_id>
docker rmi -f streamverse
```

*Important: Replace `<container_id>` with the actual container ID.*

### 2. Security Scanning

#### Install SonarQube for static code analysis:

```bash
docker run -d --name sonar -p 9000:9000 sonarqube:lts-community
```

Access SonarQube via `http://<your-ip>:9000` (default username & password: `admin`).

#### Install Trivy for container vulnerability scanning:

```bash
sudo apt-get install wget apt-transport-https gnupg lsb-release
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
echo deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install trivy
```

Scan an image with Trivy:

```bash
trivy image <image_id>
```

### 3. CI/CD Setup

#### Install Jenkins and configure the CI/CD pipeline:

##### Install Java (required for Jenkins):

```bash
sudo apt update
sudo apt install openjdk-17-jre
java -version
```

##### Install Jenkins:

```bash
wget -O /usr/share/keyrings/jenkins-keyring.asc https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
sudo systemctl start jenkins
sudo systemctl enable jenkins
```

Access Jenkins via `http://<your-ip>:8080`.

#### Configure Jenkins with necessary plugins:

* SonarQube Scanner
* NodeJS Plugin
* Docker Plugin
* OWASP Dependency-Check Plugin

#### Example Jenkins Pipeline:

```groovy
pipeline {
    agent any
    tools {
        jdk 'jdk17'
        nodejs 'node16'
    }
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/induanoopsia/StreamVerse.git'
            }
        }
        stage("SonarQube Analysis") {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh '$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=StreamVerse -Dsonar.projectKey=StreamVerse'
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }
        stage('Docker Build & Push') {
            steps {
                script {
                    sh "docker build --build-arg TMDB_V3_API_KEY=<your-api-key> -t streamverse ."
                    sh "docker push induanoopsia/streamverse:latest"
                }
            }
        }
    }
}
```

### 4. Monitoring with Prometheus and Grafana

#### Install Prometheus:

```bash
wget https://github.com/prometheus/prometheus/releases/download/v2.47.1/prometheus-2.47.1.linux-amd64.tar.gz
tar -xvf prometheus-2.47.1.linux-amd64.tar.gz
sudo mv prometheus promtool /usr/local/bin/
sudo mv prometheus.yml /etc/prometheus/
```

Configure Prometheus as a service:

```bash
sudo nano /etc/systemd/system/prometheus.service
```

Add the necessary configuration to start Prometheus as a systemd service.

#### Install Grafana:

```bash
sudo apt-get install -y grafana
sudo systemctl enable grafana-server
sudo systemctl start grafana-server
```

Access Grafana via `http://<your-ip>:3000`.

#### Configure Prometheus Data Source in Grafana:

* Go to **Grafana → Configuration → Data Sources**.
* Select **Prometheus** and set the URL to `http://localhost:9090`.

#### Add Prometheus Dashboards:

* Import pre-configured dashboards for Node Exporter (ID: 1860) and Jenkins (ID: 9964) for easy monitoring.

---

## 📢 Usage

### CI/CD Automation:

* Push your changes to the repository, and Jenkins will automatically build and deploy the application.
* The application is containerized and can be run on any Docker-enabled host.

### Security:

* Every push triggers **SonarQube** and **Trivy** scans to ensure your code and containers are secure.

### Monitoring:

* View your application’s performance and metrics in **Grafana** by visualizing data from **Prometheus**.

---

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

---

## 📝 Contributing

Feel free to fork this project, open issues, and submit pull requests. Contributions are welcome!

---

## 🙏 Acknowledgments

* **Docker** for containerizing the app.
* **SonarQube**, **Trivy**, and **OWASP Dependency-Check** for security scanning.
* **Jenkins** for CI/CD automation.
* **Prometheus** and **Grafana** for monitoring.

```



