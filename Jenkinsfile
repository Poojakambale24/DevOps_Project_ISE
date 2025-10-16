pipeline {
    agent any

    environment {
        registry = "24032004/devops_ise_project"
        registryCredential = "dockerhub-creded" // updated to your Jenkins credential ID
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
                    env.DOCKER_IMAGE_NAME = "${registry}:latest"
                    docker.build(env.DOCKER_IMAGE_NAME)
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('', registryCredential) {
                        docker.image(env.DOCKER_IMAGE_NAME).push('latest')
                        docker.image(env.DOCKER_IMAGE_NAME).push("${env.BUILD_NUMBER}")
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
