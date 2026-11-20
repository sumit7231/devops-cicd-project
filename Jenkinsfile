
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
                sh 'docker build -t devops-cicd-app:${BUILD_NUMBER} .'
            }
        }

        stage('Test Application') {
            steps {
                sh 'docker image inspect devops-cicd-app:${BUILD_NUMBER}'
                echo 'Docker image build test passed'
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                    docker stop devops-app-v3 || true
                    docker rm devops-app-v3 || true
                    docker run -d --name devops-app-v3 -p 8082:80 devops-cicd-app:${BUILD_NUMBER}
                '''
            }
        }
    }
}
