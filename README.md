# ☸️ Turing Machine Simulator – Kubernetes CI/CD with Helm & Jenkins

This project demonstrates a **production-style DevOps pipeline** for deploying the **React Turing Machine Simulator** into a Kubernetes cluster using:

- 🐳 Docker (Containerization)
- ☸️ Kubernetes (Minikube – Local Cluster)
- ⛵ Helm (Kubernetes Package Manager)
- 🔄 Jenkins (CI/CD Automation)
- 📈 Horizontal Pod Autoscaling (HPA)
- 🧪 Automated Testing (CI Quality Gate)

---

## 🏗 Architecture Overview
```bash
GitHub Push
     ↓
Jenkins Pipeline
     ↓
Run Tests (CI)
     ↓
Build Docker Image
     ↓
Push to Docker Hub
     ↓
Helm Upgrade/Install
     ↓
Kubernetes Deployment
     ↓
Horizontal Pod Autoscaling
```
## 📦 Tech Stack
- React + TypeScript (Application)
- Docker (Containerization)
- Kubernetes (Container Orchestration)
- Helm (K8s Package Management)
- Jenkins (CI/CD Automation)
- Minikube (Local Kubernetes Cluster)

## 📁 Project Structure
```bash
turing-machine-k8s/
│
├── turing-machine/            # Helm chart
│   ├── Chart.yaml
│   ├── values.yaml
│   ├── templates/
│   │   ├── deployment.yaml
│   │   ├── service.yaml
│   │   ├── hpa.yaml
│   │   └── ingress.yaml
│
├── Jenkinsfile
└── README.md
```
## 🚀 Setup Guide (Local Environment)
### 1️⃣ Start Minikube
```bash
minikube start --driver=docker
minikube addons enable metrics-server
```
Verify:
```bash
kubectl get nodes
```
### 2️⃣ Install Helm
```bash
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```
Check version:
```bash
helm version
```
### 3️⃣ Install the Helm Chart 
```bash
helm install turing-machine ./turing-machine
```
Check resources:
```bash
kubectl get pods
kubectl get svc
kubectl get hpa
```
Access Service
```bash
minikube service turing-machine
```
## 📈 Horizontal Pod Autoscaling
Auto scaling is enabled using:
- CPU-based metrics
- Metrics server (Minikube addon)
Configuration is controlled inside:
```bash
values.yaml
```
Example:
```bash
autoscaling:
  enabled: true
  minReplicas: 2
  maxReplicas: 5
  targetCPUUtilizationPercentage: 50
```
To monitor scaling:
```bash
kubectl get hpa
kubectl get pods
```
# 🔄 Jenkins CI/CD Pipeline

The pipeline performs:

1. **Clone**: Pulls the latest Node.js application code.
2. **Install**: Sets up dependencies using `npm install`.
3. **Test**: Runs automated test cases to ensure stability.
4. **Build & Push** (Paused): These stages are currently commented out in the `Jenkinsfile` to use existing images from Docker Hub.
5. **Deploy**: Uses **Helm** to perform an `upgrade --install` of the `turing-machine` chart to the cluster.

## Build Jenkins Pipeline
**You need to start the docker engine which contains Jenkins+Docker+Helm and other neccessary tools for build the automation in jenkins**
## 1️⃣ Run Jenkins in Docker
```bash
docker run -d \
  --name jenkins \
  --network host \
  -p 8080:8080 \
  -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock \
  devops8080/jenkins-with-docker-kubectl:latest
```
> [!WARNING]
> ### Docker Permission Issue
> If you delete or recreate the Jenkins container, you may encounter permission issues with the Docker socket. 
> 
> **To resolve this, run the following commands:**
> **First check the group ownership of Jenkins**
> ```bash
> docker exec jenkins ls -ln /var/run/docker.sock
> ```
> You will get a output like the below example
> ```bash
> srw-rw---- 1 root 137 0 Dec 18 17:59 /var/run/docker.sock
> ```
> Now enter the Jenkins container
> ```bash
> docker exec -it -u root jenkins bash
> then check the permission and group
> ```bash
> id jenkins
> ```
> Now you will able to see a output like this
> ```bash
> uid=1000(jenkins) gid=1000(jenkins) groups=1000(jenkins),999(docker)
> ```
> If you 999 (docker) matches your group ownership then you do not need to do anything later. If it does not match then run those command
> ```bash
> groupdel docker
> groupadd -g 137(this id will be output from this command docker exec jenkins ls -ln /var/run/docker.sock) docker
> usermod -aG docker jenkins
> id jenkins
> exit
> ```
> Restart the jenkins container
> ```bash
> docker restart jenkins
> ```

> *Note: This step must be repeated every time the container is recreated to ensure Jenkins has access to the Docker daemon.*
## 2️⃣ Access Jenkins
open: http://localhost:8080 or http://127.0.0.1:8080

## 3️⃣ Now to create a pipeline follow the below stages
- New Item
- Pipeline
- Pipeline script from SCM
- Add GitHub [Repo](https://github.com/sharfuzzaman/k8s-turing-machine-automation)
- Build Now

**For helm chart i am using one secret, generate this secret by running command on your local machine**
```bash
kubectl config view --flatten --minify > jenkins-kubeconfig
```
Then upload it to the credentials and as secret file and name it **k8s-file** in jenkins.

# 📈 Horizontal Pod Autoscaling
Already configured in values.yaml file in helm configuration.

# 🧪 CI Testing
Tests run before Docker build.

If tests fail:
- Image is not built
- Deployment does not proceed

**After successfull deployment how you will view the applications**
```bash
minikube service turing-machine --url
```
# 🔐 DevOps Concepts Demonstrated

- Docker-in-Docker Jenkins setup
- CI/CD pipeline automation
- Secure credential handling
- Helm templating
- Kubernetes deployment automation
- Rolling updates
- Horizontal scaling
- Infrastructure as Code
- Local production-like cluster setup

# 🎯 What This Project Demonstrates

- This project shows practical understanding of:
- Cloud-native deployment workflows
- Automated CI/CD pipelines
- Scalable Kubernetes architecture
- Helm packaging and versioning
- Production-style DevOps practices

# 👨‍💻 Author

Developed as an advanced DevOps practice project using Dockerized Jenkins, Helm packaging, and Kubernetes auto-scaling on Minikube.





