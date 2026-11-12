# k8-for-mongoDB

This repository contains a minimal Kubernetes setup for MongoDB and Mongo Express.

## Prerequisites

- A working Kubernetes cluster or local Kubernetes environment such as Minikube or Docker Desktop
- `kubectl` installed and configured

## Deploy

Apply the secret first, then the database, then the Mongo Express resources:

```bash
kubectl apply -f mongo-secret.yaml
kubectl apply -f mongo-deployment.yaml
kubectl apply -f mongo-express-configMap.yaml
kubectl apply -f mongo-express-deployment.yaml
```

## Verify

```bash
kubectl get pods
kubectl get svc
kubectl get pvc
kubectl describe deployment mongo
kubectl describe deployment mongo-express
```

## Access

- MongoDB is exposed through the `mongo-service` service on port `27017`
- Mongo Express is exposed through the `mongo-express-service` service on port `8081`

If you are using a local cluster such as Minikube or Docker Desktop, you can open the Mongo Express UI through the service port or use port-forwarding:

```bash
kubectl port-forward service/mongo-express-service 8081:8081
```

Then open:

```text
http://localhost:8081
```

## Notes

- The secret file is for demonstration purposes only.
- Data is stored in a persistent volume claim so it survives pod restarts.
- The Mongo Express config is read from the ConfigMap, and credentials come from the secret.
- For production, use a secure secret management workflow and stronger operational settings such as health checks and resource limits.
