pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        DOCKER_IMAGE = 'aws-cicd-demo'
        ECR_REPO = 'YOUR_ECR_REPO_URI'  // e.g., 1234567890.dkr.ecr.ap-south-1.amazonaws.com/aws-cicd-demo
    }

    stages {
        stage('Checkout SCM') {
            steps {
                git 'https://github.com/darshan2004s/aws-cicd-demo.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${DOCKER_IMAGE}")
                }
            }
        }

        stage('Login to AWS ECR') {
            steps {
                script {
                    sh """
                    aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $ECR_REPO
                    """
                }
            }
        }

        stage('Push to ECR') {
            steps {
                script {
                    sh """
                    docker tag ${DOCKER_IMAGE}:latest ${ECR_REPO}:latest
                    docker push ${ECR_REPO}:latest
                    """
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                script {
                    sh """
                    ssh -i /path/to/your/key.pem ec2-user@<EC2-PUBLIC-IP> "
                        docker pull ${ECR_REPO}:latest &&
                        docker stop myapp || true &&
                        docker rm myapp || true &&
                        docker run -d -p 5000:5000 --name myapp ${ECR_REPO}:latest
                    "
                    """
                }
            }
        }
    }
}
