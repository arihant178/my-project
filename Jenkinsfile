pipeline {
    agent any

    environment {
        APP_NAME = "simple-java-app"
        CONTAINER_NAME = "dev-java-app"
        HOST_PORT = "8081"
        IMAGE_TAG = "dev"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE') {
                        sh 'docker build -t ${APP_NAME}:${IMAGE_TAG} .'
                    }
                }
            }
        }

        stage('Stop Old Container') {
            steps {
                script {
                    catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE') {
                        sh 'docker stop ${CONTAINER_NAME} || true'
                        sh 'docker rm ${CONTAINER_NAME} || true'
                    }
                }
            }
        }

        stage('Run New Container') {
            steps {
                script {
                    catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE') {
                        sh 'docker run -d --name ${CONTAINER_NAME} -p ${HOST_PORT}:8080 ${APP_NAME}:${IMAGE_TAG}'
                    }
                }
            }
        }

        stage('Verify') {
            steps {
                script {
                    def status = sh(script: "docker ps | grep ${CONTAINER_NAME}", returnStatus: true)
                    
                    if (status != 0) {
                        echo "Container not running properly"
                        currentBuild.result = 'UNSTABLE'
                    } else {
                        echo "Container is running successfully"
                    }
                }
            }
        }
    }

    post {
        success {
            echo "Deployment SUCCESS for DEV"
        }
        unstable {
            echo "Deployment PARTIALLY SUCCESS (80% rule applied)"
        }
        failure {
            echo "Deployment FAILED"
        }
    }
}
