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

