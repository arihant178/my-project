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
                    if (env.BRANCH_NAME == "dev") {
                        env.CONTAINER_NAME = "dev-java-app"
                        env.HOST_PORT = "8081"
                        env.IMAGE_TAG = "dev"
                        env.DEPLOY_ALLOWED = "true"
                    } else if (env.BRANCH_NAME == "test") {
                        env.CONTAINER_NAME = "test-java-app"
                        env.HOST_PORT = "8082"
                        env.IMAGE_TAG = "test"
                        env.DEPLOY_ALLOWED = "true"
                    } else if (env.BRANCH_NAME == "qat") {
                        env.CONTAINER_NAME = "qat-java-app"
                        env.HOST_PORT = "8083"
                        env.IMAGE_TAG = "qat"
                        env.DEPLOY_ALLOWED = "true"
                    } else if (env.BRANCH_NAME == "main") {
                        env.CONTAINER_NAME = "prod-java-app"
                        env.HOST_PORT = "8084"
                        env.IMAGE_TAG = "main"
                        env.DEPLOY_ALLOWED = "true"
                    } else {
                        echo "Branch ${env.BRANCH_NAME} is not configured for deployment"
                        env.DEPLOY_ALLOWED = "false"
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t ${APP_NAME}:${IMAGE_TAG} .'
            }
        }

        stage('Stop Old Container') {
            when {
                expression { env.DEPLOY_ALLOWED == "true" }
            }
            steps {
                sh 'docker stop ${CONTAINER_NAME} || true'
                sh 'docker rm ${CONTAINER_NAME} || true'
            }
        }

        stage('Run New Container') {
            when {
                expression { env.DEPLOY_ALLOWED == "true" }
            }
            steps {
                sh 'docker run -d --name ${CONTAINER_NAME} -p ${HOST_PORT}:8080 ${APP_NAME}:${IMAGE_TAG}'
            }
        }

        stage('Verify') {
            when {
                expression { env.DEPLOY_ALLOWED == "true" }
            }
            steps {
                sh 'docker ps | grep ${CONTAINER_NAME}'
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
