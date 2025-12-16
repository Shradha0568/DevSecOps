pipeline {
  agent any

  environment {
    IMAGE_NAME = "shradha91103/netflix-ui-clone"
    IMAGE_TAG  = "${BUILD_NUMBER}"
  }

  stages {

    stage('Checkout Code') {
      steps {
        checkout scm
      }
    }

    stage('Build Docker Image') {
      steps {
        sh '''
          docker build -t $IMAGE_NAME:$IMAGE_TAG -t $IMAGE_NAME:latest DevSecOps
        '''
      }
    }

    stage('Security Scan - Trivy') {
      steps {
        sh '''
          trivy image $IMAGE_NAME:$IMAGE_TAG || true
        '''
      }
    }

    stage('Push Image to DockerHub') {
      steps {
        withCredentials([
          usernamePassword(
            credentialsId: 'dockerhub-creds',
            usernameVariable: 'DOCKER_USER',
            passwordVariable: 'DOCKER_PASS'
          )
        ]) {
          sh '''
            echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin

            docker push $IMAGE_NAME:$IMAGE_TAG
            docker push $IMAGE_NAME:latest

            docker logout
          '''
        }
      }
    }
  }

  post {
    always {
      sh '''
        docker image prune -f
      '''
    }
  }
}

