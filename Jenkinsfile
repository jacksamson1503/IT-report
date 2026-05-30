pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "jack1503/daily-work-log"
        IMAGE_TAG = "${BUILD_NUMBER}"
        CONTAINER_NAME = "daily-work-log-container"
    }

    stages {

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE:$IMAGE_TAG .'
            }
        }

        stage('Login to DockerHub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'USERNAME',
                    passwordVariable: 'PASSWORD'
                )]) {
                    sh 'echo $PASSWORD | docker login -u $USERNAME --password-stdin'
                }
            }
        }

        stage('Push Image to DockerHub') {
            steps {
                sh 'docker push $DOCKER_IMAGE:$IMAGE_TAG'
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                docker rm -f $CONTAINER_NAME || true

                docker run -d \
                --name $CONTAINER_NAME \
                -p 80:80 \
                $DOCKER_IMAGE:$IMAGE_TAG
                '''
            }
        }
    }

    post {
        success {
            echo 'Deployment Successful!'
        }

        failure {
            echo 'Deployment Failed!'
        }
    }
}
