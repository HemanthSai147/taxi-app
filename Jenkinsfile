pipeline {
    agent any 
    stages {
        stage('checkout Code') {
            steps {
                sh 'checkout scm'
            }
        }
        stage('Build docker image') {
            steps {
                sh 'docker build -t hemanthreddy147/apache:v1.0 .'
            }
        }
        stage('Build Docker image') {
            steps {
                sh 'docker build -t apache:v1.0 .'
            }
        }
        stage('Push docker image to DockerHub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker-cred', 
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS')]) {
                        sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                        sh 'docker tag apache:v1.0 $DOCKER_USER/apache:v1.0'
                        sh 'docker push $DOCKER_USER/apache:v1.0'
                    }
            }
        }  
        stage('Run docker container') {
            steps {
                sh 'docker run -d -p 1212:80 --name apache-container $DOCKER_USER/apache:v1.0'
            }
        }
    }
}
