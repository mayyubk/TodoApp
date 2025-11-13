pipeline {
    agent any

    environment {
        APP_NAME = "todoapp"
        CONTAINER_NAME = "todoapp_container"
        IMAGE_TAG = "latest"
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Cloning source code...'
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                echo 'Installing npm dependencies...'
                sh 'npm install --legacy-peer-deps'
            }
        }

        stage('Run Tests') {
            steps {
                echo 'Running tests...'
                sh 'npm test -- --watchAll=false'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh "docker build -t ${APP_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Deploy to Localhost') {
            steps {
                echo 'Deploying container locally...'
                sh '''
                    # Stop and remove any existing container
                    docker ps -q --filter "name=${CONTAINER_NAME}" | grep -q . && docker stop ${CONTAINER_NAME} && docker rm ${CONTAINER_NAME} || true

                    # Run new container
                    docker run -d -p 3000:80 --name ${CONTAINER_NAME} ${APP_NAME}:${IMAGE_TAG}
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Deployment successful! Your app is running at http://localhost:3000"
        }
        failure {
            echo "❌ Build or deployment failed. Check Jenkins logs."
        }
    }
}
