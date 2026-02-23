// pipeline {
//     agent any

//     environment {
//         DOCKER_IMAGE = "devops8080/react-turing-machine-nginx"
//     }

//     stages {

//         stage('Clone Repo') {
//             steps {
//                 git 'https://github.com/sharfuzzaman/react-turing-simulator_SOLID.git'
//             }
//         }
//         stage('Install Dependencies') {
//             steps {
//                 sh 'npm install'
//             }
//         }

//         stage('Run Tests') {
//             steps {
//                 sh 'npm run test'
//             }
//         }
//         stage('Build Docker Image') {
//             steps {
//                 script {
//                     sh "docker build -t $DOCKER_IMAGE:latest ."
//                 }
//             }
//         }
//         stage('Push to Docker Hub') {
//             steps {
//                 withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
//                     sh "docker login -u ${USER} -p ${PASS}"
//                     sh "docker push devops8080/react-turing-machine-nginx:latest"
//                 }
//             }
//         }
//         stage('Setup K8s Tools') {
//             steps {
//                 script {
//                 sh 'curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"'
//                 sh "chmod +x ./kubectl"
//                 sh "mkdir -p ./bin"
//                 sh "mv ./kubectl ./bin/kubectl"
//                 }
//             }
//         }
//         // stage('Deploy to Minikube') {
//         //     steps {
//         //         sh "export KUBECONFIG=/var/jenkins_home/.kube/config"
//         //         sh "kubectl apply -f k8s/"
//         //     }
//         // }
//         stage('Deploy with Helm') {
//             steps {
//                 sh 'pwd'
//                 sh 'ls -F'
//                 sh 'find . -name "Chart.yaml"'
//                 withCredentials([file(credentialsId: 'k8s-config', variable: 'KUBECONFIG')]) {
//                     sh 'helm upgrade --install turing-machine ./turing-machine-chart'
//                 }
//             }
//         }
//     }
// }
pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "devops8080/react-turing-machine-nginx"
    }

    stages {
        stage('Clone Node App') {
            steps {
                // Clone the app into a folder named 'app-code' 
                // This keeps 'turing-machine-chart' safe in the root
                dir('app-code') {
                    git 'https://github.com/sharfuzzaman/react-turing-simulator_SOLID.git'
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                // Move into the app folder to run npm
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

        // NOTE: You don't need 'Setup K8s Tools' stage anymore 
        // because we already installed them in the Dockerfile!

        stage('Deploy with Helm') {
            steps {
                // Now 'turing-machine-chart' will still be here!
                withCredentials([file(credentialsId: 'k8s-config', variable: 'KUBECONFIG')]) {
                    sh 'helm upgrade --install turing-machine ./turing-machine-chart'
                }
            }
        }
    }
}
