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
                    // Define dockerImage using 'def' to avoid scope warnings
                    def dockerImage = docker.build("${registry}:latest")
                    // Save dockerImage to env for next stage
                    env.DOCKER_IMAGE_NAME = "${registry}:latest"
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    // Rebuild dockerImage object using env variable
                    def dockerImage = docker.image("${env.DOCKER_IMAGE_NAME}")
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
