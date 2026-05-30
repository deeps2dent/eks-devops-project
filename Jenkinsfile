pipeline {
    agent any

    environment {
        AWS_REGION = "us-east-1"
        ECR_REPO = "339712988423.dkr.ecr.us-east-1.amazonaws.com/eks-app"
        CLUSTER_NAME = "eks-devops-cluster"
        IMAGE_TAG = "1.0"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Image') {
            steps {
                sh 'docker build -t eks-app:$IMAGE_TAG ./app'
            }
        }

        stage('Login to ECR') {
            steps {
                sh '''
                export AWS_PAGER=""
                aws ecr get-login-password --region $AWS_REGION \
                | docker login --username AWS --password-stdin $ECR_REPO
                '''
            }
        }

        stage('Push Image') {
            steps {
                sh '''
                docker tag eks-app:$IMAGE_TAG $ECR_REPO:$IMAGE_TAG
                docker push $ECR_REPO:$IMAGE_TAG
                '''
            }
        }

        stage('Deploy to EKS') {
            steps {
                sh '''
                export AWS_PAGER=""
                aws eks update-kubeconfig --region $AWS_REGION --name $CLUSTER_NAME
                kubectl apply -f k8s/deployment.yaml
                kubectl apply -f k8s/service.yaml
                '''
            }
        }
    }
}