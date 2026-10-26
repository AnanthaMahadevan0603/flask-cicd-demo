pipeline {
    agent any

    environment {
        BUCKET = 'anantha-jenkins-artifacts-2026'
        AWS_DEFAULT_REGION = 'ap-south-1'
    }

    triggers {
        githubPush()
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install') {
            steps {
                sh '''
                    python3 -m venv venv
                    . venv/bin/activate
                    pip install -r requirements.txt
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                    . venv/bin/activate
                    pytest
                '''
            }
        }

        stage('Package') {
            steps {
                sh 'zip -r app.zip . -x "venv/*" ".git/*" "__pycache__/*" ".pytest_cache/*"'
            }
        }

        stage('Upload to S3') {
            steps {
                sh 'aws s3 cp app.zip s3://${BUCKET}/builds/${BUILD_NUMBER}/app.zip'
            }
        }
    }

    post {
        success {
            echo "Build ${BUILD_NUMBER} uploaded to s3://${BUCKET}/builds/${BUILD_NUMBER}/app.zip"
        }
        failure {
            echo "Build ${BUILD_NUMBER} failed"
        }
    }
}
