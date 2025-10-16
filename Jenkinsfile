pipeline {
    agent any
    tools {
        maven "MAVEN3"
        jdk "OracleJDK8"
    }
    environment {
        registry = "24032004/devops_ise_project"
        registryCredential = "dockerhub-credentials"
        DOCKER_TLS_VERIFY = '1'
        DOCKER_HOST = 'tcp://127.0.0.1:55523'
        DOCKER_CERT_PATH = 'C:\\Users\\pooja\\.minikube\\certs'
        MINIKUBE_ACTIVE_DOCKERD = 'minikube'
    }
    stages {
        stage('Build') {
            steps {
                bat 'mvn clean install -DskipTests'
            }
            post {
                success {
                    echo "Archiving WAR files"
                    archiveArtifacts artifacts: '**/*.war'
                }
            }
        }

        stage('Test') {
            steps {
                bat 'mvn test'
            }
        }

        stage('Checkstyle Analysis') {
            steps {
                bat 'mvn checkstyle:checkstyle'
            }
        }

        stage('Sonar Analysis') {
            environment {
                scannerHome = tool 'sonar4.8'
            }
            steps {
                withSonarQubeEnv('Sonar-server') {
                    bat """${scannerHome}\\bin\\sonar-scanner.bat -Dsonar.projectKey=vprofile \
                    -Dsonar.projectName=vprofile \
                    -Dsonar.projectVersion=1.0 \
                    -Dsonar.sources=src/ \
                    -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                    -Dsonar.junit.reportsPath=target/surefire-reports/ \
                    -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                    -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml"""
                }
            }
        }

        stage("Quality Gate") {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Build & Push Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${registry}:$BUILD_NUMBER")
                    docker.withRegistry('', registryCredential) {
                        dockerImage.push("$BUILD_NUMBER")
                        dockerImage.push('latest')
                    }
                }
            }
        }

        stage('Clean Docker Images') {
            steps {
                bat "docker rmi ${registry}:${BUILD_NUMBER}"
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                bat "helm upgrade --install --force vprofile-stack helm/vprofilecharts --set appimage=${registry}:${BUILD_NUMBER} --namespace prod"
            }
        }
    }
}
