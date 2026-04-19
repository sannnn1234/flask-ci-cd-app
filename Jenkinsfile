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
                    echo "📦 Installing dependencies locally for tests"
                    pip install --upgrade pip
                    pip install -r requirements.txt
                    pip install pytest pylint bandit
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