pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "kamalfarah/hello-nginx:10.2"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'git@github.com:kamalfarah/hello-nginx.git',
                    credentialsId: 'github-ssh-key'   // <-- Add this credential in Jenkins
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-cred',
                                                  usernameVariable: 'DOCKER_USER',
                                                  passwordVariable: 'DOCKER_PASS')]) {
                    sh """
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    """
                }
            }
        }

        stage('Build Docker image') {
            steps {
                sh "docker build -t $DOCKER_IMAGE ."
            }
        }

        stage('Push Docker image') {
            steps {
                sh "docker push $DOCKER_IMAGE"
            }
        }
    }

    post {
        always {
            script {
                node {
                    sh 'docker logout || true'
                }
            }
        }
    }
}

