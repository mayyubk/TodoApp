pipeline {
    agent any
    environment {
        APP_NAME = "todoapp1"
        IMAGE_TAG = "latest"
        CONTAINER_NAME = "todoapp_container"
        HOST_PORT = "3000"
        CONTAINER_PORT = "80"
        USER_NAME = "ayyubkhan"
        DOCKER_HUB_CREDENTIALS = credentials('dockerhub-credentials') // Add this in Jenkins
    }
    stages {
        stage('Checkout') {
            steps {
                echo "📥 Checking out source code...."
                checkout scm
            }
        }
        stage('Build Docker Image') {
            steps {
                echo "🐳 Building Docker image: ${USER_NAME}/${APP_NAME}:${IMAGE_TAG}"
                sh "docker build -t ${USER_NAME}/${APP_NAME}:${IMAGE_TAG} ."
            }
        }
        stage('Push to Docker Hub') {
            steps {
                echo "🚀 Pushing image to Docker Hub: ${USER_NAME}/${APP_NAME}:${IMAGE_TAG}"
                script {
                    // Login to Docker Hub
                    sh "echo \$DOCKER_HUB_CREDENTIALS_PSW | docker login -u \$DOCKER_HUB_CREDENTIALS_USR --password-stdin"
                    // Push the image
                    sh "docker push ${USER_NAME}/${APP_NAME}:${IMAGE_TAG}"
                }
            }
        }
        stage('Deploy Container') {
            steps {
                echo "🚢 Deploying container: ${CONTAINER_NAME}"
                sh """
                    # Stop and remove existing container if it exists
                    docker stop ${CONTAINER_NAME} || true
                    docker rm ${CONTAINER_NAME} || true
                    
                    # Run new container
                    docker run -d --name ${CONTAINER_NAME} -p ${HOST_PORT}:${CONTAINER_PORT} ${USER_NAME}/${APP_NAME}:${IMAGE_TAG}
                """
            }
        }
    }
    post {
        always {
            echo "🧹 Cleaning up..."
            sh "docker logout"
        }
        success {
            echo "✅ Pipeline completed successfully!"
        }
        failure {
            echo "❌ Pipeline failed!"
        }
    }
}
