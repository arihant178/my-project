pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Building project...'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
            }
        }

        stage('Quality Gate') {
            steps {
                script {
                    def successRate = 85
                    if (successRate < 80) {
                        error("Quality check failed (below 80%)")
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploying to ${env.BRANCH_NAME}"
            }
        }
    }
}
