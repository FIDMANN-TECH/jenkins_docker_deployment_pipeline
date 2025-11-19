# Jenkins Docker NGINX Pipeline Project (Amazon Linux 2023)

## 🔥 Overview
This project demonstrates a complete CI/CD pipeline using **Jenkins**, **Docker**, and **NGINX** running on an **Amazon Linux 2023 EC2 instance**.  
The pipeline automatically:
- Clones a GitHub repository
- Builds a Docker image using a Dockerfile
- Runs an NGINX container serving a custom `index.html` file on port **8081**
- Ensures the deployment is consistent and reproducible

This README includes:
- Correct directory structure
- Final Jenkinsfile
- All required screenshots (with proper names)
- Full step‑by‑step setup instructions

---

# 📁 Project Directory Structure (GitHub)

```
jenkins-pipeline-app/
│
├── Dockerfile
├── index.html
├── Jenkinsfile
└── README.md
```

---

# 🚀 Final Jenkinsfile

```groovy
pipeline {
    agent any

    stages {

        stage('Clone Repository') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/YourUserName/jenkins-pipeline-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t nginx-pipeline .'
                sh 'docker images | grep nginx-pipeline || true'
            }
        }

        stage('Stop Old Container') {
            steps {
                sh '''
                if [ "$(docker ps -q --filter name=nginx-container | wc -l)" -gt 0 ]; then
                    docker stop nginx-container || true
                    docker rm nginx-container || true
                fi
                '''
            }
        }

        stage('Run Container on Port 8081') {
            steps {
                sh 'docker run -d -p 8081:80 --name nginx-container nginx-pipeline'
            }
        }

        stage('Confirm Running Deployment') {
            steps {
                sh 'docker ps'
            }
        }
    }

    post {
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline failed — check console output.'
        }
    }
}
```

---

# 🛠 Setup Instructions (Start → Finish)

## 1️⃣ Launch EC2 (Amazon Linux 2023)
Open ports:
- 22 (SSH)
- 8080 (Jenkins)
- 8081 (NGINX app)

### 📸 Screenshot 01 — EC2 Instance Running  
`01-ec2-instance-running.png`

### 📸 Screenshot 02 — Security Group Ports  
`02-ec2-security-group-ports.png`

---

## 2️⃣ Install Docker
```bash
sudo dnf update -y
sudo dnf install docker -y
sudo systemctl enable docker
sudo systemctl start docker
sudo usermod -aG docker ec2-user
```

Reconnect to apply group changes.

### 📸 Screenshot 03 — Docker Version  
`03-docker-version-output.png`

---

## 3️⃣ Install Jenkins

```bash
sudo dnf update -y
sudo dnf install java-17-amazon-corretto -y

sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo

sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key

sudo dnf install jenkins -y
sudo systemctl enable jenkins
sudo systemctl start jenkins
```

### 📸 Screenshot 04 — Jenkins Status  
`04-jenkins-status-active.png`

### 📸 Screenshot 05 — Jenkins Unlock Page  
`05-jenkins-unlock-page.png`

---

## 4️⃣ Create Project Files (on EC2 or GitHub)

### **index.html**
```html
<!DOCTYPE html>
<html>
<head>
    <title>Jenkins Docker Pipeline</title>
</head>
<body>
    <h1>Welcome to my NGINX Deployment using Jenkins Pipeline!</h1>
</body>
</html>
```

### **Dockerfile**
```dockerfile
FROM nginx:latest
WORKDIR /usr/share/nginx/html/
COPY index.html /usr/share/nginx/html/
EXPOSE 80
```

### 📸 Screenshot 06 — index.html in Vim  
`06-index-html-in-vim.png`

### 📸 Screenshot 07 — Dockerfile in Vim  
`07-dockerfile-in-vim.png`

### 📸 Screenshot 08 — GitHub Repo  
`08-github-repository-files.png`

---

## 5️⃣ Manual Docker Test (Recommended Before Jenkins)

```bash
docker build -t nginx-pipeline .
docker run -d -p 8081:80 --name nginx-container nginx-pipeline
docker ps
```

### 📸 Screenshot 14 — Manual Docker Build  
`14-manual-docker-build.png`

### 📸 Screenshot 15 — Manual Docker Images  
`15-manual-docker-images.png`

### 📸 Screenshot 16 — Manual Docker Run  
`16-manual-docker-run.png`

### 📸 Screenshot 17 — Manual Docker PS  
`17-manual-docker-ps.png`

---

## 6️⃣ Create Jenkins Pipeline Job

### 📸 Screenshot 09 — Pipeline Configuration  
`09-jenkins-pipeline-configuration.png`

### 📸 Screenshot 10 — Jenkins Console Output Success  
`10-jenkins-console-output-success.png`

### 📸 Screenshot 12 — Docker PS After Pipeline  
`12-docker-ps-running-container.png`

---

## 7️⃣ Access NGINX Web App

Go to:
```
http://EC2-Public-IP:8081
```

### 📸 Screenshot 13 — Webpage Running  
`13-nginx-webpage-running-on-8081.png`

---

# 🎉 Project Complete!
You now have a fully automated CI/CD pipeline using Jenkins, Docker, and NGINX running on EC2.

---

# 📦 Downloadable README.md
A downloadable version of this file is included.
