pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out source code from GitHub'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image'
            }
        }

        stage('Test') {
            steps {
                echo 'Testing application'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deployment stage'
            }
        }
    }
}