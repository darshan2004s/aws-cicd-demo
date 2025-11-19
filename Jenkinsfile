pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        DOCKER_IMAGE = 'aws-cicd-demo'
        ECR_REPO = '356712705564.dkr.ecr.ap-south-1.amazonaws.com/aws-cicd-demo'  // Your actual ECR repo URI
        EC2_HOST = 'ubuntu@3.89.231.219'  // Replace with EC2 public IP or DNS
        KEY_PATH = 'C:\\Users\\darsh\\Downloads\\cicdpipeline.ppk'
  // Use double \\ for Windows paths
    }

    stages {

        stage('Checkout SCM') {
            steps {
                echo '📦 Checking out source code from GitHub...'
                git branch: 'main', url: 'https://github.com/darshan2004s/aws-cicd-demo.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo '🐳 Building Docker image...'
                script {
                    bat "docker build -t ${DOCKER_IMAGE} ."
                }
            }
        }

        stage('Login to AWS ECR') {
            steps {
                echo '🔐 Logging into AWS ECR...'
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-credentials']]) {
                    script {
                        bat """
                        aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${ECR_REPO}
                        """
                    }
                }
            }
        }

        stage('Push to ECR') {
            steps {
                echo '⬆️ Pushing Docker image to ECR...'
                script {
                    bat """
                    docker tag ${DOCKER_IMAGE}:latest ${ECR_REPO}:latest
                    docker push ${ECR_REPO}:latest
                    """
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                echo '🚀 Deploying container on EC2...'
                    script {
                        bat """
                            "C:\\Program Files\\PuTTY\\plink.exe" -i "${KEY_PATH}" ${EC2_HOST} "
                            aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin ${ECR_REPO};
                            docker pull ${ECR_REPO}:latest;
                            docker stop myapp || true;
                            docker rm myapp || true;
                            docker run -d -p 5000:5000 --name myapp ${ECR_REPO}:latest
                            "
                        """
                    }
            }
        }


    }

    post {
        success {
            echo '✅ CI/CD pipeline executed successfully!'
        }
        failure {
            echo '❌ Pipeline failed! Please check Jenkins logs.'
        }
    }
}
