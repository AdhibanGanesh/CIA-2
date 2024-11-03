pipeline {
    agent any
    environment {
        AWS_CREDENTIALS = credentials('aws-access-key') 
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
                    // Stops any running container based on the "app" image to free up the port
                    sh 'docker stop $(docker ps -q --filter "ancestor=app") || true'
                    // Builds the Docker image with the tag "app"
                    sh 'docker build -t app .'
                }
            }
        }
        stage('Authenticate with ECR') {
            steps {
                script {
                    // Logs in to ECR, storing the login command and running it
                    sh '''
                    aws configure set aws_access_key_id $AWS_CREDENTIALS_USR
                    aws configure set aws_secret_access_key $AWS_CREDENTIALS_PSW
                    aws configure set region $AWS_REGION
                    $(aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $ECR_REPO_URI)
                    '''
                }
            }
        }
        stage('Tag and Push Docker Image to ECR') {
            steps {
                script {
                    // Tags and pushes the Docker image to ECR
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
                        // Prints AWS credentials for debugging purposes
                        sh 'echo $AWS_ACCESS_KEY_ID'
                        sh 'echo $AWS_SECRET_ACCESS_KEY'
                        
                        // Deploys to EC2 instance, replace with actual EC2 details
                        sh '''
                        ssh -o StrictHostKeyChecking=no -i /Users/adhibangn/Downloads/app-key.pem ec2-user@<65.1.107.79> "
                        docker pull $ECR_REPO_URI:latest &&
                        docker stop $(docker ps -q --filter ancestor=$ECR_REPO_URI:latest) || true &&
                        docker run -d -p 8082:80 $ECR_REPO_URI:latest"
                        '''
                    }
                }
            }
        }
    }
    post {
        always {
            // Cleanup or notifications if needed
            echo 'Pipeline completed.'
        }
        failure {
            // Actions to take if pipeline fails
            echo 'Pipeline failed.'
        }
    }
}
