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
- Kubernetes ConfigMaps and configuration management
- ConfigMap-backed volumes and container volume mounts
- Custom NGINX webpage deployment without rebuilding the container image
- kubectl troubleshooting and inspection
- Application connectivity testing
- Git version control and GitHub project management

## Architecture

```text
                 User / Browser
                       |
                       v
                NodePort Service
                    Port 80
                       |
                       v
                nginx-deployment
                       |
            +----------+----------+
            |          |          |
         NGINX Pod  NGINX Pod  NGINX Pod
            |          |          |
            +----------+----------+
                       |
                       v
              ConfigMap Volume
                       |
                       v
             nginx-html-config
                       |
                       v
                   index.html
```

The Kubernetes Service uses a label selector to route traffic to the NGINX Pods managed by the Deployment.

## Project Structure

```text
kubernetes-nginx-platform-lab/
├── README.md
└── manifests/
    ├── namespace.yaml
    ├── configmap.yaml
    ├── deployment.yaml
    └── service.yaml
```

## Kubernetes Namespace

The application resources are deployed into a dedicated `nginx-platform` namespace to logically isolate the project from resources in the default namespace.

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: nginx-platform
```

Create the namespace:

```bash
kubectl apply -f manifests/namespace.yaml
```

Verify the namespace:

```bash
kubectl get namespaces
kubectl get all -n nginx-platform
```
## Kubernetes ConfigMap

The application uses a Kubernetes ConfigMap to manage a custom NGINX webpage separately from the container image.

The ConfigMap `nginx-html-config` stores an `index.html` file in the `nginx-platform` namespace.

Create the ConfigMap:

```bash
kubectl apply --dry-run=client -f manifests/configmap.yaml
kubectl apply -f manifests/configmap.yaml
```

Verify the ConfigMap:

```bash
kubectl get configmaps -n nginx-platform
kubectl describe configmap nginx-html-config -n nginx-platform
```

The NGINX Deployment mounts the ConfigMap as a read-only volume at `/usr/share/nginx/html`. This allows NGINX to serve the custom webpage without rebuilding the container image.

The integration was tested by successfully rolling out three updated NGINX Pods and accessing the custom webpage through the Minikube NodePort Service.

## Kubernetes Deployment

The NGINX Deployment is configured with three replicas.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  namespace: nginx-platform
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
          volumeMounts:
            - name: nginx-html-volume
              mountPath: /usr/share/nginx/html
              readOnly: true
      volumes:
        - name: nginx-html-volume
          configMap:
            name: nginx-html-config
```

Apply the Deployment:

```bash
kubectl apply -f manifests/deployment.yaml
```

Verify the Deployment and Pods:

```bash
kubectl get deployments -n nginx-platform
kubectl get pods -n nginx-platform
```

## Kubernetes Service

The application is exposed using a NodePort Service.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-deployment
  namespace: nginx-platform
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
kubectl get services -n nginx-platform
```

## Accessing the Application

When running the cluster with Minikube:

```bash
minikube service nginx-deployment -n nginx-platform --url
```

Open the generated URL in a browser to access the NGINX application.

## Useful Kubernetes Commands

Inspect Kubernetes resources:

```bash
kubectl get pods -n nginx-platform
kubectl get deployments -n nginx-platform
kubectl get services -n nginx-platform
kubectl describe deployment nginx-deployment -n nginx-platform
kubectl describe service nginx-deployment -n nginx-platform
```

Inspect Pod logs:

```bash
kubectl logs <pod-name> -n nginx-platform
```

Scale the application:

```bash
kubectl scale deployment nginx-deployment --replicas=5 -n nginx-platform
```

Verify scaling:

```bash
kubectl get pods -n nginx-platform
```

## Skills Demonstrated

This project demonstrates practical experience with:

- Kubernetes
- Minikube
- kubectl
- NGINX
- Kubernetes Deployments
- Kubernetes Namespaces
- Kubernetes ConfigMaps
- ConfigMap-backed volumes and volume mounts
- Kubernetes rolling Deployment updates
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

- Secrets
- Persistent Volumes
- Ingress
- Resource requests and limits
- Liveness and readiness probes
- Rolling updates and rollbacks
- Helm
- Kubernetes monitoring
- CI/CD with GitHub Actions

## Author

**ikeyulestica**

Building hands-on Kubernetes, cloud, and platform engineering skills through practical projects.