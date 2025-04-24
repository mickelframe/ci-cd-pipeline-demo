pipeline {
    agent any  // Runs on any available agent

    environment {
        DOCKER_IMAGE = "mframe123/reformed-bookstore"  // Change this to your Docker image name
        DOCKER_CREDENTIALS = "docker-hub-credentials"  // Ensure you have Docker Hub credentials set in Jenkins
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/mickelframe/ci-cd-pipeline-demo.git'  // Your repo
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}:latest")
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://registry-1.docker.io/v2/', DOCKER_CREDENTIALS) {
                        echo "Logged in to Docker Hub"
                    }
                }
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS) {
                        docker.image("${DOCKER_IMAGE}:latest").push()
                    }
                }
            }
        }

        stage('Deploy Container') {
            steps {
                script {
                    // Stop running container (if exists)
                    sh "docker stop reformed-bookstore-container || true"  // Changed container name
                    sh "docker rm reformed-bookstore-container || true"

                    // Run the new container
                    sh "docker run -d --name reformed-bookstore-container -p 80:80 ${DOCKER_IMAGE}:latest"
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment Successful!'
        }
        failure {
            echo 'Deployment Failed!'
        }
    }
}
