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

2. Containerize the Web App
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
cd data-web-app
docker build -t data-web-app .
docker tag data-web-app mattregojos/data-web-app:latest
docker push mattregojos/data-web-app:latest
cd ..
```
