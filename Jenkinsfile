pipeline {
    agent any

    environment {
        APP_NAME = "simple-java-app"
        CONTAINER_NAME = ""
        HOST_PORT = ""
        IMAGE_TAG = ""
        DEPLOY_ALLOWED = "false"
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
                    def containerName = ""
                    def hostPort = ""
                    def imageTag = ""
                    def deployAllowed = "false"

                    if (env.BRANCH_NAME == "dev") {
                        containerName = "dev-java-app"
                        hostPort = "8081"
                        imageTag = "dev"
                        deployAllowed = "true"
                    } else if (env.BRANCH_NAME == "test") {
                        containerName = "test-java-app"
                        hostPort = "8082"
                        imageTag = "test"
                        deployAllowed = "true"
                    } else if (env.BRANCH_NAME == "qat") {
                        containerName = "qat-java-app"
                        hostPort = "8083"
                        imageTag = "qat"
                        deployAllowed = "true"
                    } else if (env.BRANCH_NAME == "main") {
                        containerName = "prod-java-app"
                        hostPort = "8084"
                        imageTag = "main"
                        deployAllowed = "true"
                    } else {
                        echo "Branch ${env.BRANCH_NAME} is not configured for deployment"
                    }

                    env.CONTAINER_NAME = containerName
                    env.HOST_PORT = hostPort
                    env.IMAGE_TAG = imageTag
                    env.DEPLOY_ALLOWED = deployAllowed

                    echo "BRANCH_NAME=${env.BRANCH_NAME}"
                    echo "CONTAINER_NAME=${env.CONTAINER_NAME}"
                    echo "HOST_PORT=${env.HOST_PORT}"
                    echo "IMAGE_TAG=${env.IMAGE_TAG}"
                    echo "DEPLOY_ALLOWED=${env.DEPLOY_ALLOWED}"
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${env.APP_NAME}:${env.IMAGE_TAG} ."
            }
        }

        stage('Stop Old Container') {
            when {
                expression { env.DEPLOY_ALLOWED == "true" }
            }
            steps {
                sh "docker stop ${env.CONTAINER_NAME} || true"
                sh "docker rm ${env.CONTAINER_NAME} || true"
            }
        }

        stage('Run New Container') {
            when {
                expression { env.DEPLOY_ALLOWED == "true" }
            }
            steps {
                sh "docker run -d --name ${env.CONTAINER_NAME} -p ${env.HOST_PORT}:8080 ${env.APP_NAME}:${env.IMAGE_TAG}"
            }
        }

        stage('Verify') {
            when {
                expression { env.DEPLOY_ALLOWED == "true" }
            }
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
