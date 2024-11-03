pipeline {
    agent any
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
        stage('Authenticate with AWS ECR') {
            steps {
                script {
                    withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'aws-credentials', usernameVariable: 'AWS_ACCESS_KEY_ID', passwordVariable: 'AWS_SECRET_ACCESS_KEY']]) {
                    script {
                        sh 'aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 905418202348.dkr.ecr.ap-south-1.amazonaws.com'
                    }
                }
                }
            }
        }
        stage('Tag Docker Image for ECR') {
            steps {
                script {
                    sh '''
                    docker tag app:latest 905418202348.dkr.ecr.ap-south-1.amazonaws.com/app:latest
                    '''
                }
            }
        }
        stage('Push Docker Image to ECR') {
            steps {
                script {
                    sh '''
                    docker push 905418202348.dkr.ecr.ap-south-1.amazonaws.com/app:latest
                    '''
                }
            }
        }
        stage('Deploy to EC2') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'aws-credentials', usernameVariable: 'AWS_ACCESS_KEY_ID', passwordVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                        sh '''
                        ssh -i /Users/adhibangn/Downloads/app-key.pem ec2-user@65.1.107.79 "docker run -d -p 80:80 905418202348.dkr.ecr.ap-south-1.amazonaws.com/app:latest"
                        '''
                    }
                }
            }
        }
    }
}
