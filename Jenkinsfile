pipeline {
    agent any

    environment {
        AWS_REGION   = "ap-south-1"
        ECR_HOST     = "639986415280.dkr.ecr.ap-south-1.amazonaws.com"
        ECR_REGISTRY = "639986415280.dkr.ecr.ap-south-1.amazonaws.com/my-web-app"
        IMAGE_TAG    = "v2"
    }

    stages {
        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${ECR_REGISTRY}:${IMAGE_TAG} ."
            }
        }

        stage('Push to AWS ECR') {
            steps {
                // Log in directly to the host domain, not the repository path
                sh "aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin https://${ECR_HOST}"
                sh "docker push ${ECR_REGISTRY}:${IMAGE_TAG}"
            }
        }

        stage('Deploy to server') {
            steps {
                sh "docker stop my-web-container || true"
                sh "docker rm my-web-container || true"
                sh "docker run -d -p 80:80 --name my-web-container ${ECR_REGISTRY}:${IMAGE_TAG}"
            }
        }
    }
}