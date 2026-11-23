# k8-for-mongoDB

This repository contains a minimal Kubernetes setup for MongoDB and Mongo Express in the `mongo-demo` namespace.

## Prerequisites

- A working Kubernetes cluster or local Kubernetes environment such as Minikube or Docker Desktop
- `kubectl` installed and configured

## Deploy

Create the namespace, then apply all project resources:

```bash
kubectl apply -f mongo-namespace.yaml
kubectl apply -f mongo-secret.yaml
kubectl apply -f mongo-deployment.yaml
kubectl apply -f mongo-express-configMap.yaml
kubectl apply -f mongo-express-deployment.yaml
```

## Verify

```bash
kubectl get pods -n mongo-demo
kubectl get svc -n mongo-demo
kubectl get pvc -n mongo-demo
kubectl describe deployment mongo -n mongo-demo
kubectl describe deployment mongo-express -n mongo-demo
```

## Access

- MongoDB is exposed through the `mongo-service` service on port `27017`
- Mongo Express is exposed through the `mongo-express-service` NodePort service on port `8081`

With Minikube:

```bash
minikube service mongo-express-service -n mongo-demo
```

Mongo Express UI credentials:

- Username: `admin`
- Password: `pass`

## Remove

Delete the namespace to remove the project resources and data:

```bash
kubectl delete namespace mongo-demo
```

## Notes

- The secret file is for demonstration purposes only.
- Data is stored in a persistent volume claim so it survives pod restarts.
- The Mongo Express config is read from the ConfigMap, and credentials come from the secret.
- For production, use a secure secret management workflow and stronger operational settings such as health checks and resource limits.
