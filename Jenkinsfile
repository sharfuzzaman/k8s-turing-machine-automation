pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "devops8080/react-turing-machine-nginx"
    }

    stages {
        stage('Clone Node App') {
            steps {
                dir('app-code') {
                    git 'https://github.com/sharfuzzaman/react-turing-simulator_SOLID.git'
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                dir('app-code') {
                    sh 'npm install'
                }
            }
        }

        stage('Run Tests') {
            steps {
                dir('app-code') {
                    sh 'npm run test'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                dir('app-code') {
                    sh "docker build -t $DOCKER_IMAGE:latest ."
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh "echo ${PASS} | docker login -u ${USER} --password-stdin"
                    sh "docker push ${DOCKER_IMAGE}:latest"
                }
            }
        }
        stage('Deploy with Helm') {
            sh """
                    sed -i 's/^version: .*/version: 0.1.${BUILD_NUMBER}/' ./turing-machine-chart/Chart.yaml
                    sed -i 's/^appVersion: .*/appVersion: "1.0.${BUILD_NUMBER}"/' ./turing-machine-chart/Chart.yaml
                    helm upgrade --install turing-machine ./turing-machine-chart \
                    --set image.tag=latest
                """
        }
    }
}
