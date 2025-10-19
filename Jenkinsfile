pipeline {
    agent any
    tools {
        maven 'Maven'
        jdk 'JDK11'
    }
    stages{
        stage('git checkout'){
            steps{
            git branch: 'main', url: 'https://github.com/abhishekhg1749/new-orgin.git'
            }
        }
    
        stage('compile'){
            steps{
            sh 'mvn compile' 
        }

        }
        stage('maven build'){
            steps{
            sh 'mvn clean install'
            } 
        }
        stage('Build Docker Image'){
            steps{
            sh 'docker build -t abhishekhg1749:project:1 .'
            }
        }
        stage('Docker Image Scan'){
            steps{
            sh 'trivy image --format table -o trivy-image-report.html abhishekhg1749/project:1'
            }
        }
        stage('containization'){
            steps{
            sh 'docker run -it -d --name c1 -p 9000:8080 abhishekhg17/project:1'
            }
        }
        stage('login Docker_hub'){
            steps{
            withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', passwordVariable: 'DOCKER_HUB_PASSWORD', usernameVariable: 'DOCKER_HUB_USERNAME')]) {
                sh 'echo $DOCKER_HUB_PASSWORD | docker login -u $DOCKER_HUB_USERNAME --password-stdin'
            }
            }
        stage('push Docker image to Docker Hub')
            steps{
                sh 'docker push abhishekhg17/project:1'
        }
    }
}
