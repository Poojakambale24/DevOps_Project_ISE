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
        withEnv([
            SET DOCKER_TLS_VERIFY=1
SET DOCKER_HOST=tcp://127.0.0.1:55523
SET DOCKER_CERT_PATH=C:\Users\pooja\.minikube\certs
SET MINIKUBE_ACTIVE_DOCKERD=minikube
REM To point your shell to minikube's docker-daemon, run:
REM @FOR /f "tokens=*" %i IN ('minikube -p minikube docker-env --shell cmd') DO @%i

        ]) {
            script {
                bat 'docker build -t 24032004/devops_ise_project:latest .'
                
                withDockerRegistry([credentialsId: 'dockerhub-credentials', url: '']) {
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
