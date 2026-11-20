
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

        stage('Test Docker Image') {
            steps {
                sh 'docker image inspect devops-cicd-app:${BUILD_NUMBER}'
                echo 'Docker image build test passed'
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                    OLD_CONTAINERS=$(docker ps -aq --filter "name=^devops-app-")

                    if [ -n "$OLD_CONTAINERS" ]; then
                        docker rm -f $OLD_CONTAINERS
                    fi

                    docker run -d \
                        --name devops-app-${BUILD_NUMBER} \
                        -p 8082:80 \
                        devops-cicd-app:${BUILD_NUMBER}
                '''
            }
        }

        stage('Health Check') {
            steps {
                sh '''
                    for i in 1 2 3 4 5; do
                        if curl -fsS http://localhost:8082 > /dev/null; then
                            echo "Application Health Check Passed"
                            exit 0
                        fi

                        echo "Waiting for application..."
                        sleep 3
                    done

                    echo "Application Health Check Failed"
                    exit 1
                '''
            }
        }
    }
}
