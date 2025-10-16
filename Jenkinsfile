pipeline {
    agent any
    environment {
        registry = "24032004/devops_ise_project"
        registryCredential = "dockerhub-credentials"
        DOCKER_TLS_VERIFY = "1"
        DOCKER_HOST = "tcp://127.0.0.1:55523"
        DOCKER_CERT_PATH = "C:\\Users\\pooja\\.minikube\\certs"
        MINIKUBE_ACTIVE_DOCKERD = "minikube"
    }
    stages {
        stage('Checkout SCM') {
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
                script {
                    // dockerImage is defined inside script block and accessible in this scope
                    dockerImage = docker.build("${registry}:latest")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    // Use the same dockerImage variable from previous stage
                    docker.withRegistry('', registryCredential) {
                        dockerImage.push('latest')
                        dockerImage.push("${env.BUILD_NUMBER}")
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

    post {
        always {
            echo "Pipeline finished."
        }
    }
}
