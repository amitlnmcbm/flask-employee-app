
pipeline {
    agent any

    environment {
        APP_NAME = 'flask-app'
        APP_PORT = '5000'
    }

    stages {

        stage('Checkout Code') {
            steps {
                echo 'Checking out source code...'
                checkout scm
            }
        }

        stage('Check Python Installation') {
            steps {
                sh '''
                    python3 --version
                    python3 -m pip --version
                '''
            }
        }

        stage('Create Virtual Environment') {
            steps {
                sh '''
                    python3 -m venv venv
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    . venv/bin/activate
                    python -m pip install --upgrade pip
                    pip install -r requirements.txt
                '''
            }
        }

        stage('Validate Application') {
            steps {
                sh '''
                    . venv/bin/activate
                    python -m py_compile app.py
                '''
            }
        }

        stage('Run Flask Application') {
            steps {
                sh '''
                    . venv/bin/activate
                    nohup python app.py > flask.log 2>&1 &
                    echo $! > flask.pid
                '''
            }
        }
    }

    post {
        success {
            echo 'Flask application pipeline completed successfully!'
        }

        failure {
            echo 'Pipeline failed. Please check Jenkins console output.'
        }

        always {
            echo 'Jenkins pipeline execution completed.'
        }
    }
}
