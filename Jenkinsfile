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
                // Install kubectl if not present
                sh '''
                    if ! command -v kubectl &> /dev/null; then
                        curl -LO "https://dl.k8s.io(curl -L -s https://dl.k8s.io)/bin/linux/amd64/kubectl"
                        chmod +x kubectl
                        mkdir -p ./bin
                        mv kubectl ./bin/kubectl
                    fi
                '''
                }
            }
        }
        stage('Deploy to Minikube') {
            steps {
                sh "kubectl apply -f k8s/"
            }
        }
    }
}
