pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        ECR_REPO = 'itvedant-devops-app'
    }

    stages {

        stage('Test') {
            steps {
                dir('app') {
                    sh 'mvn clean test'
                }
            }
        }

        stage('Build Application') {
            steps {
                dir('app') {
                    sh 'mvn package -DskipTests'
                }
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t $ECR_REPO:$BUILD_NUMBER .'
            }
        }

        stage('ECR Login and Push') {
            steps {
                sh '''
                    ACCOUNT_ID=$(aws sts get-caller-identity --query Account --output text)
                    REGISTRY="$ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com"

                    aws ecr get-login-password --region "$AWS_REGION" | \
                    docker login --username AWS --password-stdin "$REGISTRY"

                    docker tag "$ECR_REPO:$BUILD_NUMBER" \
                    "$REGISTRY/$ECR_REPO:$BUILD_NUMBER"

                    docker push "$REGISTRY/$ECR_REPO:$BUILD_NUMBER"
                '''
            }
        }
    }
}