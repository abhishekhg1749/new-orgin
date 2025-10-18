pipeline {
    agent any

     tools{
         jdk 'java-11'
         maven 'maven'

    environment {
        DOCKERHUB_CREDENTIALS = 'dockerhub-creds' // Jenkins credential ID for Docker Hub
        IMAGE_NAME = 'abhishek1749/project-1'
        IMAGE_TAG = "${env.BUILD_NUMBER}"  // You can also use Git commit hash
    }

    stages {
        stage('Checkout Code') {
            steps {
                // Make sure the repo contains your Dockerfile
                git branch: 'main', url: 'https://github.com/abhishekhg1749/new-orgin.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                // If Dockerfile is in a subdirectory, adjust 'dir'
                dir('.') {
                    sh "docker build -t ${abhishek1749}:${project-1} ."
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: "${DOCKERHUB_CREDENTIALS}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                sh "docker push ${abhishek1749}:${project-1}"
            }
        }
    }

    post {
        success {
            echo "Docker image ${abhishek1749}:${project-1} built and pushed successfully!"
        }
        failure {
            echo "Pipeline failed. Check logs."
        }
    }
}
