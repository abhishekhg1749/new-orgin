pipeline {
    agent any

tools{
         jdk 'java-11'
         maven 'maven'
     } 


    environment {
        DOCKERHUB_CREDENTIALS = 'dockerhub-creds' // Jenkins credential ID for Docker Hub
        IMAGE_NAME = 'abhishek1749/project-1'
        BASE_CONTAINER_NAME = 'c8'
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

                    // Push both tags
                    sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
                    sh "docker push ${IMAGE_NAME}:latest"
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                script {
                    // Dynamic container name per build
                    def dynamicContainerName = "${BASE_CONTAINER_NAME}-${env.BUILD_NUMBER}"
                    env.CONTAINER_NAME = dynamicContainerName

                    // Run new container
                    sh "docker run -d --name ${dynamicContainerName} -p 9000:8080 ${IMAGE_NAME}:${IMAGE_TAG}"
                    echo "Container '${dynamicContainerName}' is running!"

                    // Optional: Clean up old containers
                    sh """
                        docker ps -a --format '{{.Names}}' | grep '${BASE_CONTAINER_NAME}-' | sort -r | tail -n +${MAX_OLD_CONTAINERS+1} | xargs -r docker rm -f
                    """
                }
            }
        }
    }

    post {
        success {
            echo "Docker image ${IMAGE_NAME}:${IMAGE_TAG} built, pushed, and container '${CONTAINER_NAME}' is running!"
        }
        failure {
            echo "Pipeline failed. Check logs."
        }
    }
}
