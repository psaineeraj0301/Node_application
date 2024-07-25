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
                    git(
                        url: 'git@github.com:psaineeraj0301/Node_application.git',
                        credentialsId: 'Github_ssh_key'
                    )
                }
            }
        }
        stage('Build') {
            steps {
                sh 'docker build -t neeraj0307/node .'
            }
        }
        stage('Test') {
            steps {
                sh 'npm test'
            }
        }
        stage('Push') {
            steps {
                script {
                    docker.withRegistry('https://hub.docker.com/repository/docker/neeraj0307/node/general', 'dockerpass') {
                        sh 'docker push neeraj0307/node'
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    sshagent(['Github_ssh_key']) {
                        sh """
                        ssh -o StrictHostKeyChecking=no ubuntu@34.222.58.76 'docker pull neeraj0307/node'
                        ssh -o StrictHostKeyChecking=no ubuntu@34.222.58.76 'docker run -d -p 80:80 neeraj0307/node'
                        """
                    }
                }
            }
        }
    }
}
