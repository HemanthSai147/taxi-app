pipeline {
    agent any
    
    environment {
        IMAGE_NAME = "taxi-app"
        TAG = "v${BUILD_NUMBER}"
        HOST_PORT = "${BUILD_NUMBER}"
        REPOSITORY = "hemanthreddy147"
        CONTAINER_NAME = "taxi-app-${BUILD_NUMBER}"  // Unique name per build
    }
    
    stages {
        stage('Checkout') {
            steps {
              git branch: 'main', url: 'https://github.com/HemanthSai147/taxi-app.git'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:latest ."
                sh "docker tag ${IMAGE_NAME}:latest ${REPOSITORY}/${IMAGE_NAME}:${TAG}"
            }
        }
        
        stage('Push to DockerHub') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred') {
                        sh "docker push ${REPOSITORY}/${IMAGE_NAME}:${TAG}"
                    }
                }
            }
        }
        
        stage('Deploy Container') {
            steps {
                script {
                    // Stop and remove existing container with same name (if any)
                    sh """
                        docker stop ${CONTAINER_NAME} || true
                        docker rm ${CONTAINER_NAME} || true
                        docker run -d \
                            --name ${CONTAINER_NAME} \
                            -p ${HOST_PORT}:80 \
                            --restart unless-stopped \
                            ${REPOSITORY}/${IMAGE_NAME}:${TAG}
                    """
                }
            }
        }
    }
    
    post {
        always {
            script {
                // Clean up dangling images
                sh "docker image prune -f || true"
            }
        }
        success {
            echo "✅ Deployment successful! App running on port ${HOST_PORT}"
        }
        failure {
            echo "❌ Pipeline failed! Check logs for details."
        }
    }
}
