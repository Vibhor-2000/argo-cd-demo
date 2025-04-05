README - GitOps-Based Kubernetes Deployment with Argo CD

# GitOps-Based Kubernetes Deployment with Argo CD

This project demonstrates a GitOps-based approach to deploying a Kubernetes application using Argo CD.

## Prerequisites

- Docker Desktop with Kubernetes enabled
- kubectl installed and configured
- Argo CD installed and running locally
- GitHub account with a repository (e.g., https://github.com/Vibhor-2000/argo-cd-demo)

## Project Structure

```
argo-cd-demo/
│
├── my-app/
│   ├── deployment.yaml
│   └── service.yaml
```

## Step-by-Step Guide

### 1. Start Minikube or Docker Desktop Kubernetes
Ensure Kubernetes is running locally.

### 2. Install Argo CD
Use these steps for Windows PowerShell:

```powershell
curl.exe -L https://github.com/argoproj/argo-cd/releases/latest/download/argocd-windows-amd64.exe -o argocd.exe
Move-Item .\argocd.exe C:\Windows
```

### 3. Deploy Argo CD to Kubernetes

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### 4. Access Argo CD UI

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Open `http://localhost:8080` in your browser.

### 5. Login to Argo CD CLI

```powershell
argocd login localhost:8080
# Username: admin
# Password: (get it using)
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

### 6. Prepare Your App Manifests

Inside your repo:

**deployment.yaml**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app
        image: nginx
        ports:
        - containerPort: 80
```

**service.yaml**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-app-service
spec:
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: LoadBalancer
```

Push these files into your GitHub repository under a folder (e.g., `my-app`).

### 7. Create the Argo CD App

```bash
argocd app create my-app   --repo https://github.com/Vibhor-2000/argo-cd-demo.git   --path my-app   --dest-server https://kubernetes.default.svc   --dest-namespace default
```

### 8. Sync and Access the Application

```bash
argocd app sync my-app
kubectl get pods -n default
kubectl get svc -n default
```

### Done!

Your app is now deployed via Argo CD using a GitOps workflow.

---

## Author

[Vibhor-2000](https://github.com/Vibhor-2000)


