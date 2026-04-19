# Flask App CI/CD Pipeline

## Overview

This repository contains a Flask application for managing student records with MongoDB.
It includes both Jenkins and GitHub Actions pipelines for testing and deployment.

## Project Structure

```text
flask-app/
|-- app.py
|-- requirements.txt
|-- test_app.py
|-- start_flask.sh
|-- Jenkinsfile
|-- .github/workflows/deploy.yml
|-- templates/
```

## Local Setup

```bash
git clone https://github.com/sannnn1234/flask-ci-cd-app.git
cd flask-ci-cd-app
pip install -r requirements.txt
python app.py
```

The app listens on port `5005` by default.

Optional environment variables:

```bash
MONGO_URI=mongodb://localhost:27017/student_db
SECRET_KEY=change-me
PORT=5005
FLASK_DEBUG=false
```

`FLASK_DEBUG` is disabled by default and must be explicitly set to a truthy value such as `true` or `1` to enable debug mode locally.

## GitHub Actions

The workflow file is:

```text
.github/workflows/deploy.yml
```

Pipeline behavior:

1. Push to `main` or `staging`
2. Install dependencies
3. Run `pylint`, `bandit`, and `pytest`
4. Deploy to staging from `staging`
5. Deploy to production from `main`

## Jenkins Pipeline

The Jenkins pipeline performs:

1. Dependency installation
2. Lint and security scanning
3. Test execution
4. Staging deployment from `staging`
5. Production deployment from `main`

## Deployment Notes

The deployment target expects:

```bash
python3
python3-venv
python3-pip
nginx
gunicorn
```

The `start_flask.sh` script updates the staging server, installs dependencies, and restarts the `flask-app` service.

## GitHub Secrets

Configure these repository secrets for GitHub Actions:

```text
STAGING_HOST
STAGING_USER
STAGING_SSH_KEY
PROD_HOST
PROD_USER
PROD_SSH_KEY
MONGO_URI
```
=======
# Flask App CI/CD Pipeline

## Overview
This repository contains a Flask web application with automated CI/CD pipelines
using both Jenkins and GitHub Actions.

## Project Structure
```bash
flask-ci-cd-app/
│── app.py
│── requirements.txt
│── .env
│── templates/
│── .github/workflows/deploy.yml
│── start_flask.sh
│── test_app.py
│── Jenkinsfile
```
## Local Setup
```bash
git clone https://github.com/your-username/flask-ci-cd-app.git
cd flask-ci-cd-app
pip install -r requirements.txt
python app.py
```
![Local Setup](https://raw.githubusercontent.com/sannnn1234/flask-ci-cd-app/main/Screenshot/localterminal.png)

![Local Setup](https://raw.githubusercontent.com/sannnn1234/flask-ci-cd-app/main/Screenshot/localrunflaskapp.png)

## EC2 Setup
```bash
sudo apt update
sudo apt install python3-venv python3-pip nginx -y
```
##Gunicorn Setup
```bash
pip install gunicorn
gunicorn -w 3 -b 127.0.0.1:5000 app:app
```
## Systemd Service
create file
```bash
sudo nano /etc/systemd/system/flask-app.service
```
## Service Config
```bash
[Unit]
Description=Flask App
After=network.target

[Service]
User=ubuntu
WorkingDirectory=/var/www/flask-app
Environment="PATH=/var/www/flask-app/venv/bin"
ExecStart=/var/www/flask-app/venv/bin/gunicorn -w 3 -b 127.0.0.1:5000 app:app

[Install]
WantedBy=multi-user.target
```
## Start Service
```bash
sudo systemctl daemon-reload
sudo systemctl enable flask-app
sudo systemctl start flask-app
```
## Nginx Configuration
```bash
server {
    listen 80;

    location / {
        proxy_pass http://127.0.0.1:5000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```
## Enable
```bash
sudo rm /etc/nginx/sites-enabled/default
sudo ln -s /etc/nginx/sites-available/flask-app /etc/nginx/sites-enabled

sudo nginx -t
sudo systemctl restart nginx
```

## CI/CD Pipeline
```bash
.github/workflows/deploy.yml
```
![CI/CD Pipeline](https://raw.githubusercontent.com/sannnn1234/flask-ci-cd-app/main/Screenshot/deploy.png)

## GitHub Secrets
Add in GitHub → Settings → Secrets
```bash
STAGING_HOST
```
```bash
STAGING_USER
```
```bash
STAGING_SSH_KEY
```
```bash
MONGO_URI
```
![GitHub Secrets](https://raw.githubusercontent.com/sannnn1234/flask-ci-cd-app/main/Screenshot/action.png)

## Deployment Flow
1. Push to staging branch
2. GitHub Actions runs CI
3. Deploys to EC2
4. Restarts Flask service
5. Nginx serves app

![Deployment Flow](https://raw.githubusercontent.com/sannnn1234/flask-ci-cd-app/main/Screenshot/pipeline.png)

![Deployment Flow](https://raw.githubusercontent.com/sannnn1234/flask-ci-cd-app/main/Screenshot/production.png)

![Deployment Flow](https://raw.githubusercontent.com/sannnn1234/flask-ci-cd-app/main/Screenshot/stagings.png)

## Access App
```bash
http://<EC2-PUBLIC-IP>
```
![Access App](https://raw.githubusercontent.com/sannnn1234/flask-ci-cd-app/main/Screenshot/aws.png)

## Final Output
✔ CI/CD fully automated ✔ Flask app deployed ✔ Nginx reverse proxy working

## Jenkins CI/CD Pipeline
🔹 Stages
1. Install Dependencies
2. Lint & Security (pylint + bandit)
3. Run Tests (pytest)
4. Deploy Staging (branch: staging)
5. Deploy Production (branch: main)

![Jenkins CI/CD Pipeline](https://raw.githubusercontent.com/sannnn1234/flask-ci-cd-app/main/Screenshot/jenkins.png)
![Jenkins CI/CD Pipeline](https://raw.githubusercontent.com/sannnn1234/flask-ci-cd-app/main/Screenshot/jenkins2.png)
![Jenkins CI/CD Pipeline](https://raw.githubusercontent.com/sannnn1234/flask-ci-cd-app/main/Screenshot/jenkins3.png)

