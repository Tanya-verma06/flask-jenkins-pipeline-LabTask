pipeline {

    agent any

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out latest code from GitHub...'
                checkout scm
            }
        }

        stage('Create Virtual Environment') {
            steps {
                echo 'Creating Python virtual environment...'
                sh '''
                    python3 -m venv venv
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                echo 'Installing dependencies...'
                sh '''
                    ./venv/bin/python -m pip install --upgrade pip
                    ./venv/bin/pip install -r requirements.txt
                '''
            }
        }

        stage('Test') {
            steps {
                echo 'Running Flask tests...'
                sh '''
                    ./venv/bin/pytest -v
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying Flask application...'
                sh '''
                    pkill -f "gunicorn.*app:app" || true

                    nohup ./venv/bin/gunicorn \
                    --bind 0.0.0.0:5000 \
                    app:app \
                    > flask.log 2>&1 &

                    sleep 3

                    curl -f http://127.0.0.1:5000/health
                '''
            }
        }
    }

    post {

        success {
            echo 'BUILD SUCCESSFUL - Flask application deployed successfully!'
        }

        failure {
            echo 'BUILD FAILED - Check the Jenkins console output.'
        }
    }
}
