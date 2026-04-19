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

python3 -m venv venv
source venv/bin/activate

pip install -r requirements.txt
python3 app.py
```
