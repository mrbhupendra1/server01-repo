pipeline {
    agent any

    environment {
        AWS_REGION = "ap-south-1"
        AWS_ACCOUNT_ID = "207963326787"
        ECR_REPO = "my-app"
        IMAGE_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO}"
        DEPLOY_SERVER = "deploy@10.0.2.48" // Server 02 ka private IP
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/mrbhupendra1/server01-repo.git',
                    credentialsId: 'git-credentials'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t $IMAGE_URI:$BUILD_NUMBER .
                docker tag $IMAGE_URI:$BUILD_NUMBER $IMAGE_URI:latest
                '''
            }
        }

        stage('Push to ECR') {
            steps {
                withAWS(credentials: 'aws-credentials', region: "${AWS_REGION}") {
                    sh '''
                    aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $IMAGE_URI
                    docker push $IMAGE_URI:$BUILD_NUMBER
                    docker push $IMAGE_URI:latest
                    '''
                }
            }
        }

        stage('Deploy to Server 02') {
            steps {
                sh '''
                ssh -o StrictHostKeyChecking=no $DEPLOY_SERVER "
                  aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $IMAGE_URI &&
                  docker pull $IMAGE_URI:latest &&
                  docker stop myapp || true &&
                  docker rm myapp || true &&
                  docker run -d --name myapp -p 8080:8080 $IMAGE_URI:latest
                "
                '''
            }
        }
    }
}
