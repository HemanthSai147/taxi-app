pipeline {
    agent any 
    environment {
        DOCKER_CREDS = credentials('docker-cred')
    }
    stages {
        stage('checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/HemanthSai147/taxi-app.git'
            }
        }
        stage('Build docker image') {
            steps {
                sh 'docker build -t $DOCKER_CREDS_USR/apache:$BUILD_NUMBER .'
            }
        }
        stage('Push docker image to DockerHub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker-cred', 
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS')]) {
                        sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                        sh 'docker tag apache:$BUILD_NUMBER $DOCKER_USER/apache:$BUILD_NUMBER'
                        sh 'docker push $DOCKER_CREDS_USR/apache:$BUILD_NUMBER'
                    }
            }
        }  
        stage('Run docker container') {
            steps {
                sh 'docker run -d -p 1212:80 --name apache-cont${BUILDNUMBER} $DOCKER_CREDS_USR/apache:$BUILD_NUMBER'
            }
        }
    }
}
