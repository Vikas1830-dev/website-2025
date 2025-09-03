# 🚀 DevOps Lifecycle Project – CI/CD with Ansible, Jenkins & Docker

This project demonstrates a **complete DevOps lifecycle** implementation using:
- **Ansible** → Configuration Management
- **Jenkins Freestyle Jobs** → CI/CD automation
- **Docker** → Application containerization
- **GitHub Webhooks** → Automated triggers

---

## 📌 Project Objective
Abode Software wants to implement a DevOps lifecycle to automate:
1. Provisioning servers and installing required software.
2. Setting up Git workflow (`master` and `develop` branches).
3. Auto-triggering builds on code push:
   - **develop branch** → Build, Test, Deploy to Test server.
   - **master branch** → Build, Test, Build & Push Docker image, Deploy to Production.
4. Containerize the application (`hshar/webapp`) and run it via Docker.

---

## 🏗️ Architecture

Developer → GitHub → Jenkins → DockerHub → Test/Prod Servers
(Webhook) (Build/Push) (Deploy via SSH)


- **Control Node**: Jenkins + Ansible
- **Test Server**: Deploys app on `develop` branch (port 8081)
- **Prod Server**: Deploys app on `master` branch (port 8080)

---

## 🔹 Phase 1: Infrastructure Setup
- Provision **3 Ubuntu servers** (Jenkins, Test, Prod).
- Install Ansible on Jenkins node.
- Configure SSH keys for passwordless login.
- Setup Ansible inventory in `/etc/ansible/hosts`.

---

## 🔹 Phase 2: Configuration Management with Ansible
Install required software using Ansible playbook:

- **Jenkins Node (localhost)**:
  - Jenkins
  - Java (OpenJDK 11)

- **Test + Prod Servers**:
  - Docker
  - Java (OpenJDK 11)

Run:
```bash
ansible-playbook playbook.yaml

Verify:

java -version
docker --version
systemctl status jenkins


🔹 Phase 3: Jenkins Setup

Access Jenkins → http://<jenkins-ip>:8080

Install Suggested Plugins + Git, SSH Agent, Docker Pipeline, Blue Ocean.

Add Credentials:

GitHub PAT → github-creds

DockerHub user/pass → dockerhub-creds

SSH private key → ssh-servers

🔹 Phase 4: Git Workflow

Fork repo: https://github.com/hshar/website

Create develop branch:
git checkout -b develop
git push origin develop

Workflow:

develop → Deploy to Test server.

master → Deploy to Production server.


🔹 Phase 5: Jenkins Freestyle Jobs
Job 1: WebApp-Develop

Branch: develop

Steps:

Checkout code

Run tests

Deploy container to Test Server (port 8081)

Deploy script:
ssh -o StrictHostKeyChecking=no ubuntu@<test-server-ip> \
"docker rm -f webapp || true && docker run -d --name webapp -p 8081:80 hshar/webapp"


Job 2: WebApp-Master

Branch: master

Steps:

Checkout code

Run tests

Build + push Docker image

Deploy container to Production Server (port 8080)

Build & Push script:
ssh -o StrictHostKeyChecking=no ubuntu@<prod-server-ip> \
"docker rm -f webapp || true && docker run -d --name webapp -p 8080:80 $DOCKER_USER/webapp:latest"


🔹 Phase 6: GitHub Webhook

In Jenkins Job → Build Triggers → Enable GitHub hook trigger for GITScm polling.

In GitHub Repo → Settings → Webhooks → Add:
http://<jenkins-ip>:8080/github-webhook/

Select event → Push events.

✅ Now Jenkins jobs trigger automatically on code push.

✅ Final Workflow

Push to develop → Jenkins WebApp-Develop → Deploys app to Test server (8081).

Push to master → Jenkins WebApp-Master → Builds + pushes Docker image → Deploys to Production server (8080).

🌐 Demo URLs

Test Server: http://<test-server-ip>:8081

Prod Server: http://<prod-server-ip>:8080

📌 Tech Stack

Ansible

Jenkins (Freestyle Jobs)

Docker

GitHub Webhooks

📖 Author

👨‍💻 Vikas Pandey
🚀 DevOps Engineer | Cloud | CI/CD | Automation
