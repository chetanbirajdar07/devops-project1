pipeline {
    agent any

    environment {
        // Replace with your real ECR repository URL from Terraform output
        ECR_REGISTRY = "639986415280.dkr.ecr.ap-south-1.amazonaws.com/my-web-app" 
        AWS_REGION = "ap-south-1"
        IMAGE_TAG = "v2"
    }

    stages {
        stage('Build Docker Image') {
            steps {
                // Build the image and tag it directly with your full ECR URL + tag
                sh "docker build -t ${ECR_REGISTRY}:${IMAGE_TAG} ."
            }
        }

        stage('Push to AWS ECR') {
            steps {
                // 1. Authenticate Docker with your private AWS ECR registry
                sh "aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${ECR_REGISTRY}"
                
                // 2. Push the tagged image up to ECR
                sh "docker push ${ECR_REGISTRY}:${IMAGE_TAG}"
            }
        }
        stage('Deploy to server'){
            steps {
                sh "docker stop my-webpage || true"
                sh "docker rm my-webpage || true"
                sh "docker run -d -p 80:80 --name my-web-container ${ECR_REGISTRY}:${IMAGE_TAG}"
            }
        }
    }
}