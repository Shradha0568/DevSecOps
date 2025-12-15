pipeline {
  agent any

  environment {
    IMAGE_NAME = "shradha91103/netflix-ui-clone"
    IMAGE_TAG = "${BUILD_NUMBER}"
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
          docker build -t $IMAGE_NAME:$IMAGE_TAG DevSecOps
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
        withCredentials([string(credentialsId: 'dockerhub-password', variable: 'DOCKER_PASS')]) {
          sh '''
            echo $DOCKER_PASS | docker login -u shradha91103 --password-stdin
            docker push $IMAGE_NAME:$IMAGE_TAG
          '''
        }
      }
    }
  }
}

