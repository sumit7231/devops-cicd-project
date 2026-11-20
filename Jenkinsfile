pipeline {
    agent any

    environment {
        PATH = "/Applications/Docker.app/Contents/Resources/bin:/opt/homebrew/bin:/usr/local/bin:/usr/bin:/bin"
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Source code checked out from GitHub'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t devops-cicd-app:v3 .'
            }
        }

        stage('Test Application') {
            steps {
                sh 'docker image inspect devops-cicd-app:v3'
                echo 'Docker image build test passed'
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                    docker stop devops-app-v3 || true
                    docker rm devops-app-v3 || true
                    docker run -d --name devops-app-v3 -p 8082:80 devops-cicd-app:v3
                '''
            }
        }
    }
}