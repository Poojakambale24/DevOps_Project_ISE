pipeline {
  agent any
  environment {
    DOCKER_IMAGE = "24032004/devops_ise_project"
  }
  stages {
    stage('Checkout') {
      steps {
        git 'https://github.com/Poojakambale24/DevOps_Project_ISE.git'
      }
    }
    stage('Build Docker Image') {
      steps {
        script {
          dockerImage = docker.build("${DOCKER_IMAGE}:latest")
        }
      }
    }
    stage('Push to Docker Hub') {
      steps {
        script {
          docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-credentials') {
            dockerImage.push("latest")
          }
        }
      }
    }
    stage('Deploy to Kubernetes') {
      steps {
        sh 'kubectl apply -f deployment.yaml'
        sh 'kubectl apply -f service.yaml'
      }
    }
  }
  post {
    always {
      cleanWs()
    }
  }
}

