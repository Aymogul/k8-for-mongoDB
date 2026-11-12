# k8-for-mongoDB

This repository contains a minimal Kubernetes deployment for MongoDB.

## Prerequisites

- A working Kubernetes cluster or local Kubernetes environment such as Minikube or Docker Desktop
- `kubectl` installed and configured

## Deploy

```bash
kubectl apply -f mongo-secret.yaml
kubectl apply -f mongo-deployment.yaml
```

## Verify

```bash
kubectl get pods
kubectl get svc
kubectl get pvc
```

## Notes

- The secret file is for demonstration purposes only.
- Data is stored in a persistent volume claim so it survives pod restarts.
- For production, use a secure secret management workflow and stronger operational settings such as health checks and resource limits.
