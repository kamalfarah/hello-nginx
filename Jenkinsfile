pipeline {
    agent any

    environment {
        DOCKER_HUB_USERNAME = credentials('docker-hub-username')  // Jenkins credential ID
        DOCKER_HUB_PASS     = credentials('docker-hub-pass')      // Jenkins credential ID
        IMAGE               = "${DOCKER_HUB_USERNAME}/hello-nginx:10.2"
    }

    stages {
        stage('Checkout source code') {
            steps {
                checkout scm
            }
        }

        stage('Login to Docker Hub') {
            steps {
                sh 'echo $DOCKER_HUB_PASS | docker login -u $DOCKER_HUB_USERNAME --password-stdin'
            }
        }

        stage('Build Docker image') {
            steps {
                sh 'docker build -t $IMAGE .'
            }
        }

        stage('Push Docker image') {
            steps {
                sh 'docker push $IMAGE'
            }
        }
    }

    post {
        always {
            sh 'docker logout'
        }
    }
}
