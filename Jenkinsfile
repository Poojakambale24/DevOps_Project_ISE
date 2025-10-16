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

        stage('Build Docker Image') {
            steps {
                withEnv([
                    'DOCKER_TLS_VERIFY=1',
                    'DOCKER_HOST=tcp://192.168.49.2:2376',
                    'DOCKER_CERT_PATH=C:\\Users\\pooja\\.minikube\\certs',
                    'MINIKUBE_ACTIVE_DOCKERD=minikube'
                ]) {
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
