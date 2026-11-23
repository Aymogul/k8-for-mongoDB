# Kubernetes Service Types

A Kubernetes **Service** provides a stable network address for a group of Pods. Pods may restart and receive new IP addresses, while a Service keeps client traffic directed to the matching Pods.

Services normally select Pods using labels. For example:

```yaml
spec:
  selector:
    app: mongo
```

The selector should match labels on the Pods, not labels on another Kubernetes resource.

## 1. ClusterIP

`ClusterIP` is the default Service type. It exposes the Service only inside the Kubernetes cluster.

Use it for:

- communication between microservices
- databases that should not be exposed publicly
- internal APIs and backend services
- MongoDB access from Mongo Express

Example:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mongo-service
  namespace: mongo-demo
spec:
  type: ClusterIP
  selector:
    app: mongo
  ports:
    - name: mongo
      protocol: TCP
      port: 27017
      targetPort: 27017
```

Mongo Express can reach MongoDB using `mongo-service:27017` from inside the `mongo-demo` namespace.

## 2. NodePort

`NodePort` exposes the Service on a port on every node in the Kubernetes cluster. Kubernetes opens a port in the range `30000-32767` unless a different range is configured.

Use it for:

- development and testing
- local Kubernetes clusters such as Minikube
- applications that need direct node-level access
- temporary external access when no ingress or cloud load balancer is available

Example:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mongo-express-service
  namespace: mongo-demo
spec:
  type: NodePort
  selector:
    app: mongo-express
  ports:
    - name: mongo-express
      protocol: TCP
      port: 8081
      targetPort: 8081
      nodePort: 30081
```

The different ports have separate roles:

- `nodePort: 30081` is the externally accessible node port
- `port: 8081` is the stable Service port
- `targetPort: 8081` is the container port

For Minikube:

```bash
minikube service mongo-express-service -n mongo-demo
```

## 3. Headless Service

A headless Service has `clusterIP: None`. It does not provide a normal virtual IP for load balancing. Instead, DNS discovery can return the IP addresses of the selected Pods.

Use it for:

- StatefulSets
- databases that need direct Pod-to-Pod communication
- peer discovery
- applications that need to identify individual replicas
- clusters such as MongoDB replica sets

Example:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mongo-headless
  namespace: mongo-demo
spec:
  clusterIP: None
  selector:
    app: mongo
  ports:
    - name: mongo
      protocol: TCP
      port: 27017
      targetPort: 27017
```

For a StatefulSet named `mongo`, Kubernetes DNS can discover individual Pods using names such as:

```text
mongo-0.mongo-headless.mongo-demo.svc.cluster.local
mongo-1.mongo-headless.mongo-demo.svc.cluster.local
mongo-2.mongo-headless.mongo-demo.svc.cluster.local
```

A normal Deployment is usually not sufficient for this discovery pattern because Pod names are random and Pods are not individually stable.

> Note: The current MongoDB setup uses a regular `ClusterIP` Service and a single Pod. It does not need a headless Service unless MongoDB is changed to a replica set or StatefulSet.

## 4. LoadBalancer

`LoadBalancer` exposes a Service through an external load balancer. It is commonly supported by cloud providers such as AWS, Azure, Google Cloud, and other platforms with an external load-balancer controller.

Use it for:

- public web applications
- production APIs
- services that need an external IP and cloud-level load balancing
- environments that provide a LoadBalancer implementation

Example:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mongo-express-service
  namespace: mongo-demo
spec:
  type: LoadBalancer
  selector:
    app: mongo-express
  ports:
    - name: mongo-express
      protocol: TCP
      port: 8081
      targetPort: 8081
```

On a cloud cluster, the Service may receive an external IP or hostname. On a basic local cluster, the external address may remain pending unless a load-balancer addon or implementation is enabled.

## Quick Comparison

| Service type | Reachable from outside the cluster | Main use case |
|---|---:|---|
| ClusterIP | No | Internal microservice and database traffic |
| NodePort | Yes, through a node IP and port | Local development and simple external access |
| Headless | Depends on the client; no virtual cluster IP | Pod discovery for StatefulSets and distributed systems |
| LoadBalancer | Yes | External access in environments with load-balancer support |

## Relationship to This Project

- `mongo-service` uses `ClusterIP` because MongoDB should be available to Mongo Express internally.
- `mongo-express-service` uses `NodePort` so the UI can be opened locally through Minikube.
- This project does not currently use a headless Service because MongoDB is a single Deployment, not a replica set or StatefulSet.
- `LoadBalancer` would be appropriate for Mongo Express in a cloud environment, but it is unnecessary for the current local demo.

## Useful Commands

```bash
kubectl get services -n mongo-demo
kubectl describe service mongo-service -n mongo-demo
kubectl describe service mongo-express-service -n mongo-demo
kubectl get endpoints -n mongo-demo
kubectl delete namespace mongo-demo
```
