pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = credentials('dockerpass') // Docker Hub credentials
        DOCKER_IMAGE = 'neeraj0307/node'
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    // Checkout the Git repository
                    git branch: 'main', credentialsId: 'Github_ssh_key', url: 'git@github.com:psaineeraj0301/Node_application.git'
                }
            }
        }
        stage('Verify Workspace') {
            steps {
                script {
                    // List files in workspace to verify checkout
                    sh 'ls -la'
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image
                    def imageTag = "${DOCKER_IMAGE}:${env.BUILD_NUMBER}"
                    echo "Building Docker image with tag ${imageTag}"
                    docker.build(imageTag, '.')
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    // Run tests
                    echo "Running tests"
                    sh 'npm test'
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    // Push Docker image to Docker Hub
                    def imageTag = "${DOCKER_IMAGE}:${env.BUILD_NUMBER}"
                    echo "Pushing Docker image with tag ${imageTag}"
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerpass') {
                        docker.image(imageTag).push()
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    // Deploy Docker container to EC2 instance
                    sshagent(['Github_ssh_key']) {
                        def imageTag = "${DOCKER_IMAGE}:${env.BUILD_NUMBER}"
                        echo "Deploying Docker container with image ${imageTag}"
                        sh """
                        ssh -o StrictHostKeyChecking=no ubuntu@34.222.58.76 'docker pull ${imageTag}'
                        ssh -o StrictHostKeyChecking=no ubuntu@34.222.58.76 'docker run -d -p 80:80 ${imageTag}'
                        """
                    }
                }
            }
        }
    }
}

