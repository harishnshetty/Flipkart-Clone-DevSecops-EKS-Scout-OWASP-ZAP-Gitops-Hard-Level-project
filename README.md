# Flipkart-Clone-DevSecops-Terraform-EKS-Harbor-Scout-OWASP-ZAP-Gitops-Hard-Level-project


## For more projects, check out  
[https://harishnshetty.github.io/projects.html](https://harishnshetty.github.io/projects.html)

[![Video Tutorial](https://github.com/harishnshetty/image-data-project/blob/6135e01f68ebd6c691f8fc2304cfcb6d1e867dd6/ecr1.jpg)](https://youtu.be/KwKtMHBQXk4)

## OWASP ZAPROXY

[![Video Tutorial](https://github.com/harishnshetty/image-data-project/blob/6135e01f68ebd6c691f8fc2304cfcb6d1e867dd6/ecr2.jpg)](https://youtu.be/KwKtMHBQXk4)

## Jenkins Setup
- Instance Type :- c5.xlarge  [4 Cpu 8Gb Ram ] 
- 30 GB EBS
- This guide assumes an Ubuntu/Debian-like environment and sudo privileges.

---

## Ports to Enable in Security Group

- Jenkins Security Group

| Service         | Port  |
|-----------------|-------|
| HTTP            | 80    |
| HTTPS           | 443   |
| SSH             | 22    | 
| Jenkins         |       |
| SonarQube       |       |
| Prometheus      |       |
| grafana         |       |
| node_exporter   |       |

- Nexus Artifact Security Group

| Service         | Port  |
|-----------------|-------|
| SSH             | 22    | 
| Nexus           | 8081  |
| node_exporter   |       |

## System Update & Common Packages

```bash
sudo apt update
sudo apt upgrade -y

# Common tools
sudo apt install -y bash-completion wget git zip unzip curl jq net-tools build-essential ca-certificates apt-transport-https gnupg fontconfig
```
Reload bash completion if needed:
```bash
source /etc/bash_completion
```

**Install latest Git:**
```bash
sudo add-apt-repository ppa:git-core/ppa
sudo apt update
sudo apt install git -y
```

---

## Java

Install OpenJDK (choose 17 or 21 depending on your needs):

```bash
# OpenJDK 17
sudo apt install -y openjdk-17-jdk

# OR OpenJDK 21
sudo apt install -y openjdk-21-jdk
```
Verify:
```bash
java --version
```

---

## Jenkins

Official docs: https://www.jenkins.io/doc/book/installing/linux/

```bash
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null

sudo apt update
sudo apt install -y jenkins
sudo systemctl enable --now jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins
```
Initial admin password:
```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
Then open: http://your-server-ip:8080

**Note:** Jenkins requires a compatible Java runtime. Check the Jenkins documentation for supported Java versions.

---

## Docker

Official docs: https://docs.docker.com/engine/install/ubuntu/

```bash
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Add user to docker group (log out / in or newgrp to apply)
sudo usermod -aG docker $USER
newgrp docker
docker ps
```
If Jenkins needs Docker access:
```bash
sudo usermod -aG docker jenkins
sudo systemctl restart jenkins
```
Check Docker status:
```bash
sudo systemctl status docker
```

## Jenkins Plugins to Install

- Eclipse Temurin installer Plugin
- Email Extension Plugin
- OWASP Dependency-Check Plugin
- Pipeline: Stage View Plugin
- SonarQube Scanner for Jenkins
- Harbor
- Nodejs
- Pipeline Utility Steps
- Slack Notification

---
## SonarQube Docker Container Run for Analysis

sonarqube:25.10.0.114319-community

```bash
docker run -d --name sonarqube \
  -p 9000:9000 \
  -v sonarqube_data:/opt/sonarqube/data \
  -v sonarqube_logs:/opt/sonarqube/logs \
  -v sonarqube_extensions:/opt/sonarqube/extensions \
  sonarqube:25.9.0.112764-community
```
---
https://docs.docker.com/scout/install/


mkdir -p ~/.docker/cli-plugins


curl -fsSL https://raw.githubusercontent.com/docker/scout-cli/main/install.sh -o install-scout.sh
sh install-scout.sh


sudo cp ~/.docker/cli-plugins/docker-scout /usr/local/bin/docker-scout
sudo chmod +x /usr/local/bin/docker-scout
