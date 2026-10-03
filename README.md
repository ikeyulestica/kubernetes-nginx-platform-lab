# Kubernetes NGINX Platform Lab

A hands-on Kubernetes platform engineering project demonstrating how to deploy, expose, scale, inspect, and troubleshoot an NGINX application on a local Kubernetes cluster.

## Project Overview

This project documents my practical Kubernetes learning journey using Minikube and kubectl. Rather than focusing only on theory, the lab demonstrates the complete workflow of deploying and managing an application inside Kubernetes.

The project covers:

- Kubernetes Deployments and Pods
- Replica management and scaling
- Kubernetes Services
- NodePort service exposure
- Label selectors
- Declarative YAML configuration
- kubectl troubleshooting and inspection
- Application connectivity testing
- Git version control and GitHub project management

## Architecture

```text
                    User / Browser
                          |
                          |
                    NodePort Service
                       Port 80
                          |
                  nginx-deployment
                          |
              +-----------+-----------+
              |           |           |
           NGINX Pod   NGINX Pod   NGINX Pod
              |
          nginx:latest
```

The Kubernetes Service uses a label selector to route traffic to the NGINX Pods managed by the Deployment.

## Project Structure

```text
kubernetes-nginx-platform-lab/
├── README.md
└── manifests/
    ├── deployment.yaml
    └── service.yaml
```

## Kubernetes Deployment

The NGINX Deployment is configured with three replicas.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx-deployment
  template:
    metadata:
      labels:
        app: nginx-deployment
    spec:
      containers:
        - name: nginx
          image: nginx
```

Apply the Deployment:

```bash
kubectl apply -f manifests/deployment.yaml
```

Verify the Deployment and Pods:

```bash
kubectl get deployments
kubectl get pods
```

## Kubernetes Service

The application is exposed using a NodePort Service.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-deployment
spec:
  type: NodePort
  selector:
    app: nginx-deployment
  ports:
    - port: 80
      targetPort: 80
```

Apply the Service:

```bash
kubectl apply -f manifests/service.yaml
```

Verify the Service:

```bash
kubectl get services
```

## Accessing the Application

When running the cluster with Minikube:

```bash
minikube service nginx-deployment --url
```

Open the generated URL in a browser to access the NGINX application.

## Useful Kubernetes Commands

Inspect Kubernetes resources:

```bash
kubectl get pods
kubectl get deployments
kubectl get services
kubectl describe deployment nginx-deployment
kubectl describe service nginx-deployment
```

Inspect Pod logs:

```bash
kubectl logs <pod-name>
```

Scale the application:

```bash
kubectl scale deployment nginx-deployment --replicas=5
```

Verify scaling:

```bash
kubectl get pods
```

## Skills Demonstrated

This project demonstrates practical experience with:

- Kubernetes
- Minikube
- kubectl
- NGINX
- Kubernetes Deployments
- Kubernetes Services
- Pod lifecycle management
- Application scaling
- YAML manifests
- Troubleshooting Kubernetes resources
- Git
- GitHub
- Visual Studio Code

## Future Improvements

This lab will continue to evolve as I expand my Kubernetes and platform engineering skills.

Planned improvements include:

- ConfigMaps
- Secrets
- Persistent Volumes
- Ingress
- Namespaces
- Resource requests and limits
- Liveness and readiness probes
- Rolling updates and rollbacks
- Helm
- Kubernetes monitoring
- CI/CD with GitHub Actions

## Author

**ikeyulestica**

Building hands-on Kubernetes, cloud, and platform engineering skills through practical projects.