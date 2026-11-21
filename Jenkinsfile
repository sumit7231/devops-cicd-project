
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
                sh '''
                    docker build --platform linux/amd64 \
                        -t devops-cicd-app:${BUILD_NUMBER} \
                        -t woharami/devops-cicd-project:${BUILD_NUMBER} .

                    docker tag woharami/devops-cicd-project:${BUILD_NUMBER} \
                        woharami/devops-cicd-project:latest
                '''
            }
        }

        stage('Test Docker Image') {
            steps {
                sh '''
                    docker image inspect devops-cicd-app:${BUILD_NUMBER}
                    echo "Docker image build test passed"
                '''
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKERHUB_USERNAME',
                    passwordVariable: 'DOCKERHUB_PASSWORD'
                )]) {
                    sh '''
                        echo "$DOCKERHUB_PASSWORD" | docker login \
                            -u "$DOCKERHUB_USERNAME" --password-stdin

                        docker push woharami/devops-cicd-project:${BUILD_NUMBER}
                        docker push woharami/devops-cicd-project:latest

                        docker logout
                    '''
                }
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

        stage('Local Health Check') {
            steps {
                sh '''
                    for i in 1 2 3 4 5; do
                        if curl -fsS http://localhost:8082/ > /dev/null; then
                            echo "Local Application Health Check Passed"
                            exit 0
                        fi

                        echo "Waiting for local application..."
                        sleep 3
                    done

                    echo "Local Health Check Failed"
                    exit 1
                '''
            }
        }

        stage('Deploy to AWS EC2') {
            steps {
                withCredentials([sshUserPrivateKey(
                    credentialsId: 'ec2-ssh-key',
                    keyFileVariable: 'SSH_KEY',
                    usernameVariable: 'SSH_USER'
                )]) {
                    sh '''
                        ssh -i "$SSH_KEY" \
                            -o StrictHostKeyChecking=no \
                            "$SSH_USER@3.110.103.170" \
                            "BUILD_NUMBER=${BUILD_NUMBER} bash -s" <<'REMOTE'

                        set -e

                        IMAGE="woharami/devops-cicd-project:${BUILD_NUMBER}"

                        echo "Connected to AWS EC2"
                        echo "Pulling Docker image: $IMAGE"

                        sudo docker pull --platform linux/amd64 "$IMAGE"

                        echo "Removing old EC2 container..."
                        sudo docker rm -f devops-cicd-app || true

                        echo "Starting new EC2 container..."

                        sudo docker run -d \
                            --name devops-cicd-app \
                            --restart unless-stopped \
                            -p 80:80 \
                            "$IMAGE"

                        echo "Running EC2 health check..."

                        HEALTHY=0

                        for i in 1 2 3 4 5; do
                            if curl -fsS http://127.0.0.1/ > /dev/null; then
                                HEALTHY=1
                                break
                            fi

                            echo "Waiting for NGINX..."
                            sleep 3
                        done

                        if [ "$HEALTHY" -eq 1 ]; then
                            echo "EC2 Deployment Successful!"
                        else
                            echo "EC2 Health Check Failed"
                            exit 1
                        fi

                        REMOTE
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'CI/CD Pipeline Completed Successfully!'
        }

        failure {
            echo 'CI/CD Pipeline Failed. Check Console Output.'
        }
    }
}
