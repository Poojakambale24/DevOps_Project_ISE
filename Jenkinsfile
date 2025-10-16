pipeline {
    agent any

    environment {
        registry = "24032004/devops_ise_project"
        registryCredential = "dockerhub-creded" // Your Docker Hub credentials in Jenkins
        DOCKER_TLS_VERIFY = "1"
        DOCKER_HOST = "tcp://127.0.0.1:55523"
        DOCKER_CERT_PATH = "C:\\Users\\pooja\\.minikube\\certs"
        MINIKUBE_ACTIVE_DOCKERD = "minikube"
        KUBECONFIG = "C:\\Users\\pooja\\.kube\\config" // Make sure Jenkins user can access this file
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
                    // Define dockerImage and save globally using env variable
                    env.DOCKER_IMAGE_NAME = "${registry}:latest"
                    dockerImage = docker.build("${env.DOCKER_IMAGE_NAME}")
                }
            }
        }

     stage('Push Docker Image') {
    steps {
        script {
            def dockerImage = docker.image("${env.DOCKER_IMAGE_NAME}") // rebuild docker image object
            docker.withRegistry('', 'dockerhub-creded') {             // ID must match your credential
                dockerImage.push('latest')
                dockerImage.push("${env.BUILD_NUMBER}")
            }
        }
    }
}

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Use Minikube's kubectl to ensure authentication works
                    bat 'minikube kubectl -- apply -f deployment.yaml'
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
