pipeline {
    agent any

    environment {
        registry = "24032004/devops_ise_project"
        registryCredential = "dockerhub-creded"  // Docker Hub credentials ID in Jenkins
        PATH = "C:\\ProgramData\\chocolatey\\bin;C:\\Program Files\\Docker\\Docker\\resources\\bin;%PATH%"
        KUBECONFIG = "C:\\Users\\pooja\\.kube\\config"  // Ensure Jenkins user can access this
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
                    bat """
                    docker build -t %DOCKER_IMAGE_NAME% .
                    """
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('', 'dockerhub-creded') {
                        def dockerImage = docker.image("${env.DOCKER_IMAGE_NAME}")
                        dockerImage.push('latest')
                        dockerImage.push("${env.BUILD_NUMBER}")
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Assume Minikube is already running
                    bat """
                    minikube kubectl -- apply -f deployment.yaml
                    minikube kubectl -- get pods
                    """
                }
            }
        }
    }

    post {
        always {
            echo "Pipeline finished."
        }
    }
}
