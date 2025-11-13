pipeline {
    agent {
        docker {
            image 'node:20-alpine'   // Run pipeline in Node 20 environment
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }

    environment {
        APP_NAME = "todoapp"
        CONTAINER_NAME = "todoapp_container"
        IMAGE_TAG = "latest"
    }

    stages {

        stage('Checkout Code') {
            steps {
                echo '📦 Cloning repository...'
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                echo '📦 Installing npm packages...'
                sh 'npm install --legacy-peer-deps'
            }
        }

        stage('Run Tests') {
            steps {
                echo '🧪 Running unit tests...'
                sh 'npm test' // Vitest runs fine without --watchAll
            }
        }

        stage('Build Docker Image') {
            steps {
                echo '🐳 Building Docker image...'
                sh "docker build -t ${APP_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Deploy Locally') {
            steps {
                echo '🚀 Deploying to localhost...'
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
            echo "❌ Build or deployment failed. Check Jenkins logs for details."
        }
    }
}
