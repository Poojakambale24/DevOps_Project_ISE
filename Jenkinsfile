pipeline {
    agent any

    environment {
        // Minikube Docker environment variables
        DOCKER_TLS_VERIFY = '1'
        DOCKER_HOST = 'tcp://127.0.0.1:55523'
        DOCKER_CERT_PATH = 'C:\\Users\\pooja\\.minikube\\certs'
        MINIKUBE_ACTIVE_DOCKERD = 'minikube'
    }

    stages {
        stage('Checkout from Git') {
            steps {
                checkout([$class: 'GitSCM', 
                  branches: [[name: 'main']], 
                  userRemoteConfigs: [[
                    url: 'https://github.com/Poojakambale24/DevOps_Project_ISE.git',
                    credentialsId: 'github-creds'
                  ]]
                ])
            }
        }

        stage('Build & Push Docker Image') {
            steps {
                // Use Docker Hub credentials
                withDockerRegistry([credentialsId: 'dockerhub-credentials', url: '']) {
                    bat 'docker build -t 24032004/devops_ise_project:latest .'
                    bat 'docker push 24032004/devops_ise_project:latest'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                bat 'kubectl apply -f deployment.yaml'
            }
        }
    }
}
