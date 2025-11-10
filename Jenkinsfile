pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'aws-cicd-demo'
        DOCKER_TAG = 'latest'
    }

    stages {
        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                script {
                    bat """
                    docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} .
                    """
                }
            }
        }

        stage('Run Container') {
            steps {
                echo 'Running Docker container...'
                script {
                    bat """
                    docker run -d -p 8081:8080 ${DOCKER_IMAGE}:${DOCKER_TAG}
                    """
                }
            }
        }
    }

    post {
        success {
            echo '✅ Build and container deployment successful!'
        }
        failure {
            echo '❌ Build failed! Check the console logs for details.'
        }
    }
}

