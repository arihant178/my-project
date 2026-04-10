pipeline {
    agent any

    environment {
        APP_NAME = "simple-java-app"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Set Environment') {
            steps {
                script {
                    if (env.BRANCH_NAME?.trim() == "dev") {
                        env.CONTAINER_NAME = "dev-java-app"
                        env.HOST_PORT = "8081"
                        env.IMAGE_TAG = "dev"
                    } else if (env.BRANCH_NAME?.trim() == "test") {
                        env.CONTAINER_NAME = "test-java-app"
                        env.HOST_PORT = "8082"
                        env.IMAGE_TAG = "test"
                    } else if (env.BRANCH_NAME?.trim() == "qat") {
                        env.CONTAINER_NAME = "qat-java-app"
                        env.HOST_PORT = "8083"
                        env.IMAGE_TAG = "qat"
                    } else if (env.BRANCH_NAME?.trim() == "main") {
                        env.CONTAINER_NAME = "prod-java-app"
                        env.HOST_PORT = "8084"
                        env.IMAGE_TAG = "main"
                    } else {
                        error("Unsupported branch for deployment: ${env.BRANCH_NAME}")
                    }

                    echo "BRANCH_NAME=${env.BRANCH_NAME}"
                    echo "CONTAINER_NAME=${env.CONTAINER_NAME}"
                    echo "HOST_PORT=${env.HOST_PORT}"
                    echo "IMAGE_TAG=${env.IMAGE_TAG}"
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${env.APP_NAME}:${env.IMAGE_TAG} ."
            }
        }

        stage('Stop Old Container') {
            steps {
                sh "docker stop ${env.CONTAINER_NAME} || true"
                sh "docker rm ${env.CONTAINER_NAME} || true"
            }
        }

        stage('Run New Container') {
            steps {
                sh "docker run -d --name ${env.CONTAINER_NAME} -p ${env.HOST_PORT}:8080 ${env.APP_NAME}:${env.IMAGE_TAG}"
            }
        }

        stage('Verify') {
            steps {
                sh "docker ps | grep ${env.CONTAINER_NAME}"
            }
        }
    }

    post {
        success {
            echo "Deployment SUCCESS for branch ${env.BRANCH_NAME}"
        }
        failure {
            echo "Deployment FAILED for branch ${env.BRANCH_NAME}"
        }
    }
}
