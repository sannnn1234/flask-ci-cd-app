pipeline {
    agent any

    environment {
        MONGO_URI  = credentials('MONGO_URI')
        STAGING_IP = credentials('STAGING_IP')
        PROD_IP    = credentials('PROD_IP')
    }

    stages {

        /* ========================
           INSTALL DEPENDENCIES
        ======================== */
        stage('Install Dependencies') {
            steps {
                sh '''
                    python3 -m pip install --upgrade pip
                    pip install -r requirements.txt pytest pylint bandit
                '''
            }
        }

        /* ========================
           LINT + SECURITY
        ======================== */
        stage('Lint & Security') {
            steps {
                sh '''
                    pylint app.py || true
                    bandit -r . --exclude ./venv,./tests -s B101,B104 || true
                '''
            }
        }

        /* ========================
           RUN TESTS
        ======================== */
        stage('Run Tests') {
            steps {
                sh '''
                    pytest -v || true
                '''
            }
        }

        /* ========================
           DEPLOY STAGING
        ======================== */
        stage('Deploy Staging') {
            when {
                branch 'staging'
            }

            steps {
                sshagent(['staging-ssh']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@$STAGING_IP << 'EOF'
                    set -e

                    APP_DIR=/var/www/flask-app

                    echo "🚀 Deploying to STAGING..."

                    cd $APP_DIR

                    git fetch origin
                    git reset --hard origin/staging

                    source venv/bin/activate
                    pip install -r requirements.txt gunicorn

                    sudo systemctl restart flask-app
                    sudo systemctl restart nginx

                    echo "✅ STAGING DEPLOYED"
                    EOF
                    '''
                }
            }
        }

        /* ========================
           DEPLOY PRODUCTION
        ======================== */
        stage('Deploy Production') {
            when {
                branch 'main'
            }

            steps {
                sshagent(['prod-ssh']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@$PROD_IP << 'EOF'
                    set -e

                    APP_DIR=/var/www/flask-app

                    echo "🚀 Deploying to PRODUCTION..."

                    cd $APP_DIR

                    git fetch origin
                    git reset --hard origin/main

                    source venv/bin/activate
                    pip install -r requirements.txt gunicorn

                    sudo systemctl restart flask-app
                    sudo systemctl restart nginx

                    echo "✅ PRODUCTION DEPLOYED"
                    EOF
                    '''
                }
            }
        }
    }

    /* ========================
       POST ACTIONS
    ======================== */
    post {
        success {
            echo "🎉 Pipeline SUCCESS on branch: ${env.BRANCH_NAME}"
        }

        failure {
            echo "❌ Pipeline FAILED on branch: ${env.BRANCH_NAME}"
        }
    }
}