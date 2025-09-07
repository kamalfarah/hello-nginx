pipeline {
  agent any

  environment {
    REGISTRY_URL   = 'https://index.docker.io/v1/'
    IMAGE_NAME     = 'kamalfarah/hello-nginx'
  }

  options {
    timestamps()
    ansiColor('xterm')
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Docker Build') {
      steps {
        script {
          // image tagged by Jenkins build number; keep a handle for pushing
          dockerImage = docker.build("${IMAGE_NAME}:${env.BUILD_NUMBER}")
        }
      }
    }

    stage('Docker Push') {
      steps {
        script {
          docker.withRegistry(REGISTRY_URL, 'dockerhub-cred') {
            dockerImage.push("${env.BUILD_NUMBER}")
            dockerImage.push("latest")
          }
        }
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        withCredentials([file(credentialsId: 'kubeconfig-demo', variable: 'KUBECONFIG')]) {
          sh '''
            set -e
            # Apply base manifests (if first time)
            kubectl -n demo apply -f deployment.yaml
            kubectl -n demo apply -f service.yaml

            # Update the running deployment to the new exact image tag
            kubectl -n demo set image deployment/hello-nginx hello-nginx=${IMAGE_NAME}:${BUILD_NUMBER}

            # Wait for rollout to complete
            kubectl -n demo rollout status deployment/hello-nginx
          '''
        }
      }
    }
  }

  post {
    failure {
      echo "Build failed. Check the console log."
    }
    success {
      echo "Deployed ${IMAGE_NAME}:${env.BUILD_NUMBER} to namespace demo"
    }
  }
}
