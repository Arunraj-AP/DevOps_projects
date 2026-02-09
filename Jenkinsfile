pipeline {
    agent any

    environment {
        DOCKER_CREDS    = credentials('Dockerhub')
        KUBECONFIG_DATA = credentials('k3s-config')
        IMAGE_NAME      = "arunraj30/cicd"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Arunraj-AP/DevOps_projects'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh """
                  docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} .
                """
            }
        }
     stage('Login to DockerHub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'Dockerhub',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {
                    sh """
                        echo "$PASS" | docker login -u "$USER" --password-stdin
                    """
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                sh """
                  docker push ${IMAGE_NAME}:${BUILD_NUMBER}
                """
            }
        }

        stage('Deploy to K3s') {
            steps {
                withCredentials([file(credentialsId: 'k3s-config', variable: 'KCFG')]) {
                    sh """
                        export KUBECONFIG=$KCFG
                        kubectl apply -f deployment.yaml
                        kubectl apply -f service.yaml
                    """
                }
            }
        } 
    } 
}
