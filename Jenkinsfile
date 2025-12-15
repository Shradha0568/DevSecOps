pipeline {
  agent any

  environment {
    IMAGE = "shradha91103/netflix-ui-clone"
    TAG = "${BUILD_NUMBER}"
  }

  stages {

    stage('Checkout Code') {
      steps {
        checkout scm
      }
    }

    stage('Build Docker Image') {
      steps {
        dir('DevSecOps') {
          sh """
            docker build -t $IMAGE:$TAG .
          """
        }
      }
    }

    stage('Security Scan - Trivy') {
      steps {
        sh """
          trivy image --severity HIGH,CRITICAL $IMAGE:$TAG || true
        """
      }
    }

    stage('Push Image to DockerHub') {
      steps {
        withCredentials([usernamePassword(
          credentialsId: 'dockerhub-creds',
          usernameVariable: 'USER',
          passwordVariable: 'PASS'
        )]) {
          sh """
            echo $PASS | docker login -u $USER --password-stdin
            docker push $IMAGE:$TAG
          """
        }
      }
    }

    stage('Update Helm Chart') {
      steps {
        sh """
          sed -i 's/tag:.*/tag: $TAG/' helm-chart/values.yaml
          git config user.email "jenkins@local"
          git config user.name "jenkins"
          git commit -am "ci: update image tag to $TAG" || echo "No changes"
          git push origin main
        """
      }
    }
  }
}

