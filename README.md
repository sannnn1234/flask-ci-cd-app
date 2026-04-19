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
`-- templates/
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
