# Container Monitoring and Observability

## Architecture

## Objective
* To containerize and deploy a web app
* To orchestrate the container
* To monitor and observe the container

## Tech Stack
* AWS, Minikube, Kubernetes, Python, Streamlit, Helm, Prometheus, Grafana

## Tasks
Prerequisites: Docker, Minikube, Kubernetes, (Optional) EC2

1. Web App
  * Data Analysis Web App: https://github.com/mregojos/CI-CD-with-gitOps

2. Containerize the Web App and test it locally
```sh
# Data Web App
cd Application
docker build -t data-web-app .
docker run --name data-web-app -p 8501:8501 data-web-app
cd ..
```

3. Push the image to the Docker Hub
```sh
# docker logout
docker login 
# Data Web App
cd Application
docker build -t data-web-app .
docker tag data-web-app <Docker Hub User>/data-web-app:latest
docker push <Docker Hub User>/data-web-app:latest
cd ..
```

4. (Optional if not yet installed) Kubernetes and Minikube 
```sh
# Install Kubernetes and Minikube
# kubectl-minikube.sh
# chmod +x kubectl-minikube.sh
# ./kubectl-minikube.sh

# Update System
sudo apt-get update

# Install kubectl
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl

# Make it executable and move it to a system path
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl

# Install minikube
# Download
curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64

# Make it executable and move it to a system path
chmod +x minikube
sudo mv minikube /usr/local/bin/

# minikube start [--driver=docker]
# minikube status
# kubectl cluster-info
# kubectl get nodes
```

5. deployment.yaml

[deployment.yaml](https://github.com/Mregojos/Monitoring-and-Observability/blob/main/Deployment/deployment.yaml)

6. Start Minikube and apply the deployment file

```sh
# Start Minikube
minikube start

# Deployment Folder
cd deployment
# Create a namespace
kubectl create namespace data-web-app
# Apply deployment.yaml
kubectl apply -n data-web-app -f deployment.yaml
kubectl get deployments -n data-web-app
kubectl get services -n data-web-app

kubectl get namespaces
kubectl get pods -n data-web-app
kubectl get nodes -n data-web-app
kubectl get all -n data-web-app

# To watch it 
watch kubectl get all -n data-web-app

#------------------(Optional) Two Apps
# Deployment Folder
cd deployment

# First app
# Create a namespace
kubectl create namespace data-web-app
# Apply deployment.yaml
kubectl apply -n data-web-app -f data-web-app-deployment.yaml
kubectl get deployments -n data-web-app
kubectl get services -n data-web-app

kubectl get namespaces
kubectl get pods -n data-web-app
kubectl get nodes -n data-web-app
kubectl get all -n data-web-app

# To watch it 
watch kubectl get all -n data-web-app

# Second app
# Create a namespace
kubectl create namespace noteblog-web-app
# Apply deployment.yaml
kubectl apply -n noteblog-web-app -f noteblog-web-app-deployment.yaml
kubectl get deployments -n noteblog-web-app
kubectl get services -n noteblog-web-app

kubectl get namespaces
kubectl get pods -n noteblog-web-app
kubectl get nodes -n noteblog-web-app
kubectl get all -n noteblog-web-app

# To watch it 
watch kubectl get all -n noteblog-web-app
```



