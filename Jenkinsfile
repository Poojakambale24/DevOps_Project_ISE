pipeline {
    agent any

    environment {
        registry = "24032004/devops_ise_project"
        registryCredential = "dockerhub-creded" // Your Docker Hub credentials in Jenkins
        // Add Minikube and Docker to PATH
        PATH = "C:\\ProgramData\\chocolatey\\bin;C:\\Program Files\\Docker\\Docker\\resources\\bin;%PATH%"
        KUBECONFIG = "C:\\Users\\pooja\\.kube\\config" // Ensure Jenkins user can access this
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
                    // Build Docker image using Windows batch
                    bat """
                    docker build -t %DOCKER_IMAGE_NAME% .
                    """
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    bat """
                    docker login -u %DOCKER_HUB_USERNAME% -p %DOCKER_HUB_PASSWORD%
                    docker push %DOCKER_IMAGE_NAME%
                    docker tag %DOCKER_IMAGE_NAME% ${registry}:%BUILD_NUMBER%
                    docker push ${registry}:%BUILD_NUMBER%
                    """
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Ensure Jenkins can access Minikube
                    bat """
                    minikube start --driver=docker
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
