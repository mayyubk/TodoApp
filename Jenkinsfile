pipeline {
    agent any

    environment {
        APP_NAME = "todoapp"
        IMAGE_TAG = "latest"
        CONTAINER_NAME = "todoapp_container"
        HOST_PORT = "3000"
        CONTAINER_PORT = "80"
    }

    stages {
        stage('Checkout') {
            steps {
                echo "📥 Checking out source code..."
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "🐳 Building Docker image: ${APP_NAME}:${IMAGE_TAG}"
                sh "docker build -t ${APP_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Deploy Locally') {
            steps {
                echo "🚀 Deploying container: ${CONTAINER_NAME}"
                sh """
                    # Stop the container if it's running
                    if [ \$(docker ps -q -f "name=${CONTAINER_NAME}") ]; then
                        echo "Stopping running container..."
                        docker stop ${CONTAINER_NAME}
                    fi

                    # Remove the container if it exists (stopped or exited)
                    if [ \$(docker ps -aq -f "name=${CONTAINER_NAME}") ]; then
                        echo "Removing existing container..."
                        docker rm ${CONTAINER_NAME}
                    fi

                    # Run the new container
                    echo "Starting new container..."
                    docker run -d -p ${HOST_PORT}:${CONTAINER_PORT} --name ${CONTAINER_NAME} ${APP_NAME}:${IMAGE_TAG}
                """
            }
        }
    }

    post {
        success {
            echo "✅ Deployment successful! App running at http://localhost:${HOST_PORT}"
        }
        failure {
            echo "❌ Deployment failed."
        }
    }
}
