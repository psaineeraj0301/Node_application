pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = credentials('dockerpass')
        DOCKER_IMAGE = 'neeraj0307/node'
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    git branch: 'main', credentialsId: 'Github_ssh_key', url: 'git@github.com:psaineeraj0301/Node_application.git'
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                script {
                    sh 'npm install'
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    sh 'npm test'
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    def imageTag = "${DOCKER_IMAGE}:${env.BUILD_NUMBER}"
                    echo "Building Docker image with tag ${imageTag}"
                    docker.build(imageTag, '.')
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
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
