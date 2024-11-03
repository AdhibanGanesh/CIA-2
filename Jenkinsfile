pipeline {
    agent any
    environment {
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
            ste