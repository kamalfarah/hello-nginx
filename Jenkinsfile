pipeline {
    agent any

    environment {
        DOCKER_CREDS = credentials('dockerhub-cred')  // must match your Jenkins credential ID
        IMAGE = "kamalfarah/hello-nginx:10.2"
    }

    stages {
        stage('Checkout source code') {
            steps {
                checkout scm
            }
        }

        stage('Login to Docker Hub') {
            steps {
                sh 'echo $DOCKER_CREDS_PSW | docker login -u $DOCKER_CREDS_USR --password-stdin'
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
            script {
                // Avoid "MissingContextVariableException"
                sh 'docker logout || true'
            }
        }
    }
}
