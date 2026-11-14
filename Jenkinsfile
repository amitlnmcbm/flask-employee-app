
pipeline {
    agent any

    stages {

        stage('Install Python3') {
            steps {
                sh '''
                    if ! command -v python3 >/dev/null 2>&1; then
                        echo "Installing Python3..."

                        apt-get update
                        apt-get install -y python3 python3-pip python3-venv
                    fi

                    python3 --version
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    python3 -m venv venv

                    venv/bin/pip install --upgrade pip

                    venv/bin/pip install -r requirements.txt
                '''
            }
        }

        stage('Run Flask Application') {
            steps {
                sh '''
                    nohup venv/bin/python app.py > flask.log 2>&1 &

                    echo $! > flask.pid

                    sleep 5

                    if ! kill -0 $(cat flask.pid); then
                        cat flask.log
                        exit 1
                    fi
                '''
            }
        }
    }

    post {
        success {
            echo 'Flask application started successfully!'
        }

        failure {
            echo 'Pipeline execution failed!'
        }

        always {
            archiveArtifacts artifacts: 'flask.log',
                             allowEmptyArchive: true
        }
    }
}
