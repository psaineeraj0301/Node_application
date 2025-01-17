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
                sh 'ls -la'
            }
        }
        stage('Install Dependencies') {
            steps {
                dir('node_app') {
                    script {
                        // Verify the package.json file exists
                        sh 'ls -la'
                        sh 'npm install'
                    }
                }
            }
        }
        stage('Test') {
            steps {
                dir('node_app') {
                    script {
                        sh 'npm test'
                    }
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
                        ls 
                        ip r
                        ssh -o StrictHostKeyChecking=no ubuntu@172.31.16.58 'docker pull ${imageTag}'
                        ssh -o StrictHostKeyChecking=no ubuntu@172.31.16.58 'docker run -d -p 80:80 ${imageTag}'
                        """
                    }
                }
            }
        }
    }
}
