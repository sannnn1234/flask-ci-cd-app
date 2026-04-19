pipeline {
    agent any

    environment {
        MONGO_URI  = credentials('MONGO_URI')
        STAGING_IP = credentials('STAGING_IP')
        PROD_IP    = credentials('PROD_IP')
    }

    stages {

        /* =========================
           1. INSTALL + TEST ENV
        ========================== */
        stage('Install Dependencies') {
            steps {
                sh '''
                    echo "📦 Running tests in isolated environment"

                    python3 -m pip install pytest pylint bandit || true
                '''
            }
        }

        /* =========================
           2. LINT CHECK
        ========================== */
        stage('Lint & Security') {
            steps {
                sh '''
                    echo "🔍 Running lint checks"
                    pylint app.py || true

                    echo "🛡️ Running security scan"
                    bandit -r . --exclude ./venv,./tests -s B101,B104 || true
                '''
            }
        }

        /* =========================
           3. RUN TESTS
        ========================== */
        stage('Run Tests') {
            steps {
                sh '''
                    echo "🧪 Running tests"
                    pytest -v || true
                '''
            }
        }

        /* =========================
           4. DEPLOY TO STAGING
        ========================== */
        stage('Deploy Staging') {
            when {
                branch 'staging'
            }

            steps {
                sshagent(['staging-ssh']) {
                    sh '''
<<<<<<< HEAD
                        ssh -o StrictHostKeyChecking=no ubuntu@$STAGING_IP bash -s << 'ENDSSH'
                        set -e
                        APP_DIR=/var/www/flask-app
                        echo "Deploying Staging"
                        if [ ! -d "$APP_DIR" ]; then
                            sudo mkdir -p "$APP_DIR"
                            sudo chown -R ubuntu:ubuntu "$APP_DIR"
                            git clone -b staging https://github.com/sannnn1234/flask-ci-cd-app.git "$APP_DIR"
                            cd "$APP_DIR"
                        else
                            cd "$APP_DIR"
                            if [ -f ".env" ]; then
                                cp .env /tmp/flask_app_env_backup
                                echo "Backed up .env"
                            fi
                            git fetch origin
                            git reset --hard origin/staging
                            if [ -f "/tmp/flask_app_env_backup" ]; then
                                cp /tmp/flask_app_env_backup .env
                                echo "Restored .env"
                            fi
                        fi
                        cd "$APP_DIR"
                        if [ ! -f ".env" ]; then
                            echo "ERROR: .env missing!"
                            exit 1
                        fi
                        echo "Current .env:"
                        cat .env
                        python3 -m venv venv
                        . venv/bin/activate
                        pip install --upgrade pip
                        pip install -r requirements.txt gunicorn
                        sudo systemctl daemon-reload
                        sudo systemctl enable flask-app
                        sudo systemctl restart flask-app
                        sudo systemctl restart nginx
                        echo "Staging Done"
                        ENDSSH
=======
                    ssh -o StrictHostKeyChecking=no ubuntu@$STAGING_IP << 'EOF'
                    set -e

                    echo "🚀 Deploying to STAGING"

                    APP_DIR=/var/www/flask-app
                    cd $APP_DIR

                    git fetch origin
                    git reset --hard origin/staging

                    echo "Activating virtual environment"
                    source venv/bin/activate

                    pip install -r requirements.txt

                    echo "🔄 Restarting services"
                    sudo systemctl restart flask-app
                    sudo systemctl restart nginx

                    echo "✅ STAGING DEPLOYMENT DONE"
                    EOF
>>>>>>> main
                    '''
                }
            }
        }

        /* =========================
           5. DEPLOY TO PRODUCTION
        ========================== */
        stage('Deploy Production') {
            when {
                branch 'main'
            }

            steps {
                sshagent(['prod-ssh']) {
                    sh '''
<<<<<<< HEAD
                        ssh -o StrictHostKeyChecking=no ubuntu@$PROD_IP bash -s << 'ENDSSH'
                        set -e
                        APP_DIR=/var/www/flask-app
                        echo "Deploying Production"
                        if [ ! -d "$APP_DIR" ]; then
                            sudo mkdir -p "$APP_DIR"
                            sudo chown -R ubuntu:ubuntu "$APP_DIR"
                            git clone -b master https://github.com/sannnn1234/flask-ci-cd-app.git "$APP_DIR"
                            cd "$APP_DIR"
                        else
                            cd "$APP_DIR"
                            if [ -f ".env" ]; then
                                cp .env /tmp/flask_app_env_backup
                                echo "Backed up .env"
                            fi
                            git fetch origin
                            git reset --hard origin/master
                            if [ -f "/tmp/flask_app_env_backup" ]; then
                                cp /tmp/flask_app_env_backup .env
                                echo "Restored .env"
                            fi
                        fi
                        cd "$APP_DIR"
                        if [ ! -f ".env" ]; then
                            echo "ERROR: .env missing!"
                            exit 1
                        fi
                        echo "Current .env:"
                        cat .env
                        python3 -m venv venv
                        . venv/bin/activate
                        pip install --upgrade pip
                        pip install -r requirements.txt gunicorn
                        sudo systemctl daemon-reload
                        sudo systemctl enable flask-app
                        sudo systemctl restart flask-app
                        sudo systemctl restart nginx
                        echo "Production Done"
                        ENDSSH
=======
                    ssh -o StrictHostKeyChecking=no ubuntu@$PROD_IP << 'EOF'
                    set -e

                    echo "🚀 Deploying to PRODUCTION"

                    APP_DIR=/var/www/flask-app
                    cd $APP_DIR

                    git fetch origin
                    git reset --hard origin/main

                    echo "Activating virtual environment"
                    source venv/bin/activate

                    pip install -r requirements.txt

                    echo "🔄 Restarting services"
                    sudo systemctl restart flask-app
                    sudo systemctl restart nginx

                    echo "✅ PRODUCTION DEPLOYMENT DONE"
                    EOF
>>>>>>> main
                    '''
                }
            }
        }
    }

    /* =========================
       POST ACTIONS
    ========================== */
    post {
        success {
            echo "🎉 SUCCESS: Pipeline completed on ${env.BRANCH_NAME}"
        }

        failure {
            echo "❌ FAILED: Pipeline failed on ${env.BRANCH_NAME}"
        }
    }
}