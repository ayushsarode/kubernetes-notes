# Mental Model Phase 1

Welcome to Phase 1! Here we cover the absolute basics of Kubernetes, including core components, Pods, Deployments, and how networking and services tie everything together.

![Phase 1 Mental Model](image.png)

## Kubernetes Phase 1 Cheat Sheet

## Terminology

| Term                    | Description                                                          |
| ----------------------- | -------------------------------------------------------------------- |
| **Cluster**             | A collection of Control Plane and Worker Nodes.                      |
| **Control Plane**       | Manages the entire Kubernetes cluster.                               |
| **Worker Node**         | Runs application Pods.                                               |
| **API Server**          | Entry point for all Kubernetes requests.                             |
| **etcd**                | Distributed key-value database storing the cluster state.            |
| **Scheduler**           | Assigns Pods to Worker Nodes.                                        |
| **Controller Manager**  | Runs Kubernetes controllers.                                         |
| **Controller**          | Watches objects and reconciles desired state with actual state.      |
| **Reconciliation Loop** | Continuous process of matching desired state to actual state.        |
| **Desired State**       | The state defined by the user.                                       |
| **Actual State**        | The current state of the cluster.                                    |
| **Deployment**          | Manages ReplicaSets and application updates.                         |
| **ReplicaSet**          | Ensures the desired number of Pods are running.                      |
| **Pod**                 | Smallest deployable unit in Kubernetes.                              |
| **Pause Container**     | Holds the Pod's shared network namespace.                            |
| **Namespace**           | Logically groups Kubernetes resources.                               |
| **Label**               | Key-value metadata attached to resources.                            |
| **Selector**            | Finds resources with matching labels.                                |
| **Service**             | Stable endpoint for accessing Pods.                                  |
| **ClusterIP**           | Internal IP assigned to a Service.                                   |
| **CoreDNS**             | Internal DNS server for Kubernetes.                                  |
| **FQDN**                | Fully Qualified Domain Name (`service.namespace.svc.cluster.local`). |
| **ConfigMap**           | Stores non-sensitive configuration.                                  |
| **Secret**              | Stores sensitive configuration like passwords and API keys.          |
| **metadata**            | Information about an object (name, labels, namespace).               |
| **spec**                | Desired state of an object.                                          |
| **status**              | Current state maintained by Kubernetes.                              |

---

# kubectl Commands

## Cluster

```bash
kubectl cluster-info
```

Show cluster information.

```bash
kubectl version
```

Show client and server versions.

---

## Nodes

```bash
kubectl get nodes
```

List all nodes.

```bash
kubectl describe node <node-name>
```

Show detailed node information.

---

## Namespaces

```bash
kubectl get ns
```

List namespaces.

```bash
kubectl create namespace backend
```

Create a namespace.

```bash
kubectl delete namespace backend
```

Delete a namespace.

---

## Deployments

```bash
kubectl create deployment nginx --image=nginx
```

Create a Deployment.

```bash
kubectl get deployments
```

List Deployments.

```bash
kubectl describe deployment nginx
```

Show Deployment details.

```bash
kubectl delete deployment nginx
```

Delete a Deployment.

---

## ReplicaSets

```bash
kubectl get replicasets
```

List ReplicaSets.

```bash
kubectl describe replicaset <name>
```

Show ReplicaSet details.

---

## Pods

```bash
kubectl get pods
```

List Pods.

```bash
kubectl get pods -o wide
```

Show Pods with IPs and Nodes.

```bash
kubectl describe pod <pod-name>
```

Show Pod details.

```bash
kubectl logs <pod-name>
```

View Pod logs.

```bash
kubectl exec -it <pod-name> -- sh
```

Open a shell inside a Pod.

```bash
kubectl delete pod <pod-name>
```

Delete a Pod.

---

## Services

```bash
kubectl expose deployment nginx --port=80 --target-port=80
```

Expose a Deployment as a Service.

```bash
kubectl get svc
```

List Services.

```bash
kubectl describe svc nginx
```

Show Service details.

---

## DNS

```bash
kubectl run dns-test --image=busybox:1.36 -it --rm -- sh
```

Create a temporary Pod for DNS testing.

```bash
nslookup nginx
```

Resolve a Service name.

```bash
nslookup nginx.default.svc.cluster.local
```

Resolve a Service using its FQDN.

---

## ConfigMaps

```bash
kubectl create configmap app-config \
  --from-literal=PORT=8080 \
  --from-literal=LOG_LEVEL=debug
```

Create a ConfigMap.

```bash
kubectl get configmap
```

List ConfigMaps.

```bash
kubectl describe configmap app-config
```

Show ConfigMap details.

```bash
kubectl delete configmap app-config
```

Delete a ConfigMap.

---

## Secrets

```bash
kubectl create secret generic db-secret \
  --from-literal=password=super-secret
```

Create a Secret.

```bash
kubectl get secret
```

List Secrets.

```bash
kubectl describe secret db-secret
```

Show Secret details.

```bash
kubectl get secret db-secret -o yaml
```

View Secret YAML.

```bash
kubectl get secret db-secret -o jsonpath='{.data.password}' | base64 --decode
```

Decode a Secret value.

```bash
kubectl delete secret db-secret
```

Delete a Secret.
