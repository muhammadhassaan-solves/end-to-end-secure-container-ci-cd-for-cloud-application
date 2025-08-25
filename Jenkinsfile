pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'muhammadhassaansolves/docker-cicd-demo'
        DOCKER_CREDENTIALS = 'dockerhub-credentials'
        EC2_USER = 'ubuntu'
        EC2_IP = '3.82.229.176'
        EC2_SSH_CREDENTIALS = 'ec2-ssh-key'
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Test') {
            agent {
                docker {
                    image 'node:16-alpine'
                    args '-v /var/run/docker.sock:/var/run/docker.sock'
                }
            }
            steps {
                sh 'npm install'
                sh 'npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}:${BUILD_NUMBER}")
                }
            }
        }

        stage('Security Scan') {
            steps {
                sh '''
                docker run --rm \
                  -v /var/run/docker.sock:/var/run/docker.sock \
                  aquasec/trivy:latest image \
                  --exit-code 0 --severity HIGH,CRITICAL ${DOCKER_IMAGE}:${BUILD_NUMBER} || true
                '''
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', DOCKER_CREDENTIALS) {
                        def image = docker.image("${DOCKER_IMAGE}:${BUILD_NUMBER}")
                        image.push()
                        image.push("latest")
                    }
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent([EC2_SSH_CREDENTIALS]) {
                    sh """
                    ssh -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_IP} '
                      docker stop docker-cicd-staging || true
                      docker rm docker-cicd-staging || true
                      docker pull ${DOCKER_IMAGE}:${BUILD_NUMBER}
                      docker run -d --name docker-cicd-staging -p 3001:3000 ${DOCKER_IMAGE}:${BUILD_NUMBER}
                      sleep 10
                      curl -f http://localhost:3001/health
                    '
                    """
                }
            }
        }
    }
}
