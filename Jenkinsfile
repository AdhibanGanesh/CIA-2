pipeline {
    agent any
     environment {
        AWS_CREDENTIALS = credentials('aws-credentials') 
        AWS_REGION = 'ap-south-1'  
        ECR_REPO_URI = '905418202348.dkr.ecr.ap-south-1.amazonaws.com/app'
    }
    stages {
        stage('Clone Repository') {
            steps {
                git url: 'https://github.com/AdhibanGanesh/CIA-2.git', branch: 'master'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker stop $(docker ps -q --filter "ancestor=app") || true'
                    sh 'docker build -t app .'
                }
            }
        }
        stage('Authenticate with ECR') {
            steps {
                script {
                    sh '''
                    $(aws ecr get-login --no-include-email --region $AWS_REGION)
                    '''
                }
            }
        }
        stage('Tag and Push Docker Image to ECR') {
            steps {
                script {
                    sh '''
                    docker tag app:latest $ECR_REPO_URI:latest
                    docker push $ECR_REPO_URI:latest
                    '''
                }
            }
        }
        stage('Deploy to EC2 or Elastic Beanstalk') {
            steps {
                script {
                    withEnv(["AWS_ACCESS_KEY_ID=${AWS_CREDENTIALS_USR}", "AWS_SECRET_ACCESS_KEY=${AWS_CREDENTIALS_PSW}"]) {
                    // AWS CLI commands or any other deployment scripts
                        sh 'echo $AWS_ACCESS_KEY_ID'
                        sh 'echo $AWS_SECRET_ACCESS_KEY'
                    }
                    // Example: Deploying to EC2 (Assuming you have an EC2 instance running)
                    sh '''
                    # Example command to run the Docker container on EC2
                    ssh -i /Users/adhibangn/Downloads/app-key.pem ec2-user@65.1.107.79 "docker run -d -p 8082:80 $ECR_REPO_URI:latest"
                    '''
                    // OR Deploying to Elastic Beanstalk
                    // You can use AWS CLI commands to deploy to Elastic Beanstalk here.
                }
            }
        }
    }
}
