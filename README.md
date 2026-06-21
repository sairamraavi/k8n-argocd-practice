# Argo CD Practice - Nginx Deployment

This is a simple Kubernetes project for practicing Argo CD. It includes a basic nginx deployment with a NodePort service.

## What's Included

- **deployment.yml**: A simple nginx Deployment with 1 replica
- **service.yml**: A NodePort Service to expose nginx on port 30080
- **README.md**: This file

## Project Structure

```
k8n-argocd-practice/
├── deployment.yml      # nginx Deployment manifest
├── service.yml         # NodePort Service manifest
└── README.md          # Documentation
```

## Quick Start

### Option 1: Direct kubectl Apply

```bash
cd /path/to/k8n-argocd-practice
kubectl apply -f deployment.yml
kubectl apply -f service.yml
```

### Option 2: Apply All at Once

```bash
kubectl apply -f .
```

## Verify Deployment

```bash
# Check deployment status
kubectl get deployment nginx-deployment
kubectl get pods

# Check service
kubectl get service nginx-service

# Port forward to access nginx locally
kubectl port-forward service/nginx-service 8080:80
# Then open http://localhost:8080 in your browser
```

## Using with Argo CD

### Prerequisites

- Argo CD installed in your cluster (in `argocd` namespace)
- This project pushed to a Git repository accessible by Argo CD

### Create an Application

1. Replace placeholders in the command below and run:

```bash
argocd app create nginx-argocd-demo \
  --repo https://github.com/YOU/YOUR_REPO.git \
  --path DevOps/Kubernetes/Apps/k8n-argocd-practice \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace default \
  --project default
```

2. Alternatively, create an Application manifest manually:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: nginx-argocd-demo
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/YOU/YOUR_REPO.git
    path: DevOps/Kubernetes/Apps/k8n-argocd-practice
    targetRevision: HEAD
  destination:
    server: https://kubernetes.default.svc
    namespace: default
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

3. Apply the Application:

```bash
kubectl apply -f application.yaml
```

### Monitor Sync Status

```bash
# Check application status
argocd app get nginx-argocd-demo

# Watch the sync
argocd app wait nginx-argocd-demo --sync

# Or use kubectl
kubectl -n argocd get applications.argoproj.io
kubectl -n argocd get applications.argoproj.io nginx-argocd-demo -o wide
```

## Learning Goals

- Understand Kubernetes Deployments and Services
- Practice deploying applications with kubectl
- Learn Argo CD declarative GitOps workflows
- See how Argo CD syncs manifests from Git to your cluster

## Cleanup

```bash
# Remove via kubectl
kubectl delete -f .

# Or via Argo CD
argocd app delete nginx-argocd-demo
```

## Next Steps

- Modify the nginx image or replicas in `deployment.yml` and watch Argo CD sync the changes
- Add environment-specific overlays using Kustomize
- Set up webhook triggers for automated sync on Git push