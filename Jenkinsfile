
pipeline {
    agent any

    environment {
        APP_NAME = 'flask-app'
        VENV = 'venv'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Check Python') {
            steps {
                sh '''
                    echo "Checking Python installation..."

                    if ! command -v python3 >/dev/null 2>&1; then
                        echo "Python3 not found. Installing..."

                        sudo apt-get update
                        sudo apt-get install -y python3 python3-pip python3-venv
                    fi

                    python3 --version
                    python3 -m pip --version || true
                '''
            }
        }

        stage('Create Virtual Environment') {
            steps {
                sh '''
                    python3 -m venv ${VENV}

                    ${VENV}/bin/python --version

                    ${VENV}/bin/pip install --upgrade pip
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    ${VENV}/bin/pip install -r requirements.txt
                '''
            }
        }

        stage('Run Flask Application') {
            steps {
                sh '''
                    if [ -f flask.pid ]; then
                        kill $(cat flask.pid) 2>/dev/null || true
                    fi

                    nohup ${VENV}/bin/python app.py > flask.log 2>&1 &

                    echo $! > flask.pid

                    sleep 5

                    if ! kill -0 $(cat flask.pid); then
                        echo "Flask application failed to start"
                        cat flask.log
                        exit 1
                    fi

                    echo "Flask application started successfully"
                '''
            }
        }
    }

    post {
        success {
            echo 'Flask deployment completed successfully!'
        }

        failure {
            echo 'Pipeline execution failed!'
        }

        always {
            archiveArtifacts artifacts: 'flask.log', allowEmptyArchive: true
        }
    }
}
