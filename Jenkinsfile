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
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh "docker login -u ${USER} -p ${PASS}"
                    sh "docker push devops8080/react-turing-machine-nginx:latest"
                }
            }
        }
        stage('Setup K8s Tools') {
            steps {
                script {
                sh 'curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"'
                sh "chmod +x ./kubectl"
                sh "mkdir -p ./bin"
                sh "mv ./kubectl ./bin/kubectl"
                }
            }
        }
        stage('Deploy to Minikube') {
            steps {
                sh "./bin/kubectl apply -f k8s/"
            }
        }
    }
}
