pipeline {
    agent any

    environment {
        DOCKER_CREDS = credentials('docker-cred')
        IMAGE_NAME = "apache"
        CONTAINER_NAME = "apache-cont"
        PORT = "8081"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/HemanthSai147/taxi-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                    docker build -t ${IMAGE_NAME}:v${BUILD_NUMBER} .
                """
            }
        }

        stage('Login & Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker-cred',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh """
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin

                        docker tag ${IMAGE_NAME}:v${BUILD_NUMBER} $DOCKER_USER/${IMAGE_NAME}:v${BUILD_NUMBER}
                        docker push $DOCKER_USER/${IMAGE_NAME}:v${BUILD_NUMBER}
                    """
                }
            }
        }

        stage('Cleanup Old Container') {
            steps {
                sh """
                    docker rm -f ${CONTAINER_NAME} || true
                """
            }
        }

        stage('Deploy Container') {
            steps {
                sh """
                    docker run -d -p ${PORT}:80 --name ${CONTAINER_NAME} \
                    $DOCKER_USER/${IMAGE_NAME}:v${BUILD_NUMBER}
                """
            }
        }

        stage('Cleanup Images (Safe)') {
            steps {
                sh """
                    docker image prune -f
                """
            }
        }
    }
}
