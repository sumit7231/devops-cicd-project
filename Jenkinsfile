pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo 'Source code checked out from GitHub'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '/Applications/Docker.app/Contents/Resources/bin/docker build -t devops-cicd-app:v3 .'
            }
        }

        stage('Test Application') {
            steps {
                sh '/Applications/Docker.app/Contents/Resources/bin/docker image inspect devops-cicd-app:v3'
                echo 'Docker image build test passed'
            }
        }
    }
}