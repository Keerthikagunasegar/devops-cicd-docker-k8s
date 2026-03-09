pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "keerthikagunasegar/cicd-webapp"
        IMAGE_TAG = "v${BUILD_NUMBER}"
    }

    stages {

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE:$IMAGE_TAG ./webapp'
            }
        }

        stage('Login to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                }
            }
        }

        stage('Push Image to DockerHub') {
            steps {
                sh 'docker push $DOCKER_IMAGE:$IMAGE_TAG'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl set image deployment/cicd-webapp cicd-webapp=$DOCKER_IMAGE:$IMAGE_TAG'
            }
        }

        stage('Verify Deployment') {
            steps {
                sh 'kubectl rollout status deployment/cicd-webapp'
            }
        }

    }
}