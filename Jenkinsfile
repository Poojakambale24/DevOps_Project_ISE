pipeline {
    agent any
    stages {
        stage('Checkout') {
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
                // Set environment variables for Minikube's Docker
                withEnv([
                    'DOCKER_TLS_VERIFY=1',
                    'DOCKER_HOST=tcp://127.0.0.1:55523',  // update this from "minikube docker-env"
                    'DOCKER_CERT_PATH=C:\\Users\\pooja\\.minikube\\certs',
                    'MINIKUBE_ACTIVE_DOCKERD=minikube'
                ]) {
                    script {
                        // Build image
                        bat 'docker build -t 24032004/devops_ise_project:latest .'

                        // Push image to Docker Hub using Jenkins credentials
                        withDockerRegistry([credentialsId: 'dockerhub-creds', url: '']) {
                            bat 'docker push 24032004/devops_ise_project:latest'
                        }
                    }
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
