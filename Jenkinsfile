pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "devops8080/react-turing-machine-nginx"
    }

    stages {

        stage('Clone Repo') {
            steps {
                git 'https://github.com/sharfuzzaman/react-turing-simulator_SOLID.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t $DOCKER_IMAGE:latest ."
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                sh "docker push $DOCKER_IMAGE:latest"
                }
        }

        stage('Deploy to Minikube') {
            steps {
                sh "kubectl apply -f k8s/"
            }
        }
    }
}
