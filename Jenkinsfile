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
                sh 'docker build -t devops-cicd-app:v3 .'
            }
        }

        stage('Test Application') {
            steps {
                sh 'docker image inspect devops-cicd-app:v3'
                echo 'Docker image build test passed'
            }
        }
    }
}