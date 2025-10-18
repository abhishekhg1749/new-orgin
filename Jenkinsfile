pipeline {
    agent any

tools{
         jdk 'java-11'
         maven 'maven'
     } 

    environment {
        DOCKERHUB_CREDENTIALS = 'dockerhub-creds' // Jenkins credential ID
        IMAGE_NAME = 'abhishek1749/project-1'
        BASE_CONTAINER_NAME = 'con8'
        MAX_OLD_CONTAINERS = 5 // Number of old containers to keep
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/yourusername/project-1.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Get short Git commit hash
                    def gitCommit = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
                    env.IMAGE_TAG = gitCommit

                    // Build Docker image
                    sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: "${DOCKERHUB_CREDENTIALS}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh '''
                            echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        '''
                    }
                }
            }
        }

        stage('Tag and Push Docker Image') {
            steps {
                script {
                    // Tag as 'latest' too
                    sh "docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${IMAGE_NAME}:latest"
