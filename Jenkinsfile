pipeline {
    agent any

    environment {
        APP_NAME = "todoapp"
        IMAGE_TAG = "latest"
        CONTAINER_NAME = "todoapp_container"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "🐳 Building Docker image..."
                sh "docker build -t ${APP_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Deploy Locally') {
            steps {
                echo "🚀 Deploying container..."
                sh '''
                    docker ps -q --filter "name=${CONTAINER_NAME}" | grep -q . && docker stop ${CONTAINER_NAME} && docker rm ${CONTAINER_NAME} || true
                    docker run -d -p 3000:80 --name ${CONTAINER_NAME} ${APP_NAME}:${IMAGE_TAG}
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Deployment successful! App running at http://localhost:3000"
        }
        failure {
            echo "❌ Deployment failed."
        }
    }
}
