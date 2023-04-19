# Container Monitoring and Observability

## Architecture
![](https://github.com/Mregojos/Monitoring-and-Observability/blob/main/images/Architecture.png)
[](https://draw.io)

## Objective
* Containerize and deploy a web app
* Orchestrate the container
* Monitor and observe the container

## Tech Stack
* EC2, Minikube, Kubernetes, Python, Streamlit, Helm, Prometheus, Grafana

## Tasks
Prerequisites: Docker, Minikube, Kubernetes, (Optional) EC2

1. Web app
  * Data Analysis Web App: https://github.com/mregojos/CI-CD-with-gitOps

2. Containerize the web app and test it locally
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
cd Deployment
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

#------ Delete deployment and SVC
kubectl delete deployments data-web-app -n data-web-app
kubectl delete svc data-web-app-service -n data-web-app
```

![](https://github.com/Mregojos/Monitoring-and-Observability/blob/main/images/kubectl%20get%20all%20-n%20data-web-app.png)

7. View the deployed web app using port-forwarding
```sh
# Port Forwarding use the Docker Port, this will work in Cloud9
kubectl port-forward deployment/data-web-app 8501:8501 --address 0.0.0.0 -n data-web-app
```

8. Download and install Helm, Prometheus, and Grafana
```sh
mkdir helm
cd helm

# Download and Install Prometheus
wget https://get.helm.sh/helm-v3.11.3-linux-amd64.tar.gz
tar -zxvf helm-v3.11.3-linux-amd64.tar.gz
sudo mv linux-amd64/helm /usr/local/bin/helm

# Add the Prometheus Community Helm Repository
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

# Create a new namespace for Prometheus
kubectl create namespace monitoring
# Install Prometheus Operator
helm install prometheus prometheus-community/kube-prometheus-stack --namespace monitoring
# Install Grafana using helm
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm install grafana grafana/grafana --namespace monitoring

# Access Grafana Dashboard
# Username
admin
# Get the Grafana admin password
kubectl get secret --namespace monitoring grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```

9. View the Grafana Web UI
```sh
# Expose Grafana to local machine
kubectl port-forward --namespace monitoring service/grafana 3000:80 --address 0.0.0.0
```

![Grafana](https://github.com/Mregojos/Monitoring-and-Observability/blob/main/images/Grafana.png)

10. Add a metric dashboard in Grafana

```sh

# In Grafana, add Prometheus data source
http://prometheus-kube-prometheus-prometheus.monitoring:9090

#---------------For data-web-app
# CPU usage rate for each container in the data-web-app namespace
rate(container_cpu_usage_seconds_total{namespace="data-web-app"}[5m])

# Memory usage for each container in the data-web-app namespace (in MB)
container_memory_usage_bytes{namespace="data-web-app"} / 1024 / 1024

# Memory working set for each container in the data-web-app namespace
container_memory_working_set_bytes{namespace="data-web-app"}

# Number of running pods in the data-web-app namespace
count(kube_pod_status_phase{namespace="data-web-app", phase="Running"})

# Total number of succeeded pods in the data-web-app namespace
count(kube_pod_status_phase{namespace="data-web-app", phase="Succeeded"})

# Total number of unknown pods in the data-web-app namespace
count(kube_pod_status_phase{namespace="data-web-app", phase="Unknown"})

# Number of available replicas for each deployment in the data-web-app namespace
kube_deployment_status_replicas_available{namespace="data-web-app"}

# Disk read IOPS (Input/Output Operations PerSecond) for each container in the dta-web-app namespace
rate(container_fs_reads_total{namespace="data-web-app"}[5m])
```

11. Test the web app and see the metric dashboard 
![Dashboard](https://github.com/Mregojos/Monitoring-and-Observability/blob/main/images/Dashboard.png)

12. (Optional) Add notifications

13. Clean-up






























