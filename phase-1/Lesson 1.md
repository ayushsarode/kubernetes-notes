# Lesson 1: Kubernetes Core Concepts: Clusters, Nodes, and Pods

## What is Kubernetes (K8s)?

**The Problem:** Running many containers (like Docker) across multiple servers manually is a nightmare. You have to figure out where to place them, how to restart them if they crash, and how to route traffic.

**The Solution:** Kubernetes (K8s) is an orchestrator that automates all of this. It acts like an operating system for your cluster, automatically handling:
- **Placement:** Deciding which server runs which container.
- **Healing:** Restarting failed containers instantly.
- **Routing:** Directing user traffic to the right place.

## Key Concepts

### Hierarchy: Cluster -> Node(s) -> Pods
Everything we deploy eventually becomes one or more Pods.

For example:
- A **Deployment** creates Pods.
- **OpenKruise** manages Pods.
- **KubeArmor** secures Pods.
- **Dapr** injects a sidecar into Pods.
- **Kube-burner** creates thousands of Pods.

### Essential Commands We Learned
```bash
kind create cluster --name learning 
kubectl cluster-info
kubectl get nodes
kubectl get namespaces
kubectl get pods -A
kubectl describe node
```

## Definitions

### Cluster
The entire Kubernetes environment. It contains:
- One or more nodes
- Networking
- The Kubernetes API
- Everything needed to run applications

### Node
Simply a **machine** (physical or virtual). Its job is to run Pods.

Every node has:
- CPU
- Memory
- Storage
- A container runtime
- A `kubelet`

### Pod
A pod is the smallest deployable unit in Kubernetes.

A Pod usually contains:
- One application container
- Sometimes helper containers (sidecars)

Examples:
```text
Pod
└── nginx container
```
or
```text
Pod
├── Go API
└── Dapr sidecar
```

## Important Note

Pods **don't exist by themselves** in real applications. Usually, you create a **Deployment**, and the Deployment creates Pods for you.

```text
Deployment -> ReplicaSet -> Pods -> Containers
```

You'll almost never run:
```bash
kubectl run nginx
```
Instead, you'll create a **Deployment** because it ensures the desired number of Pods are always running.

## Mental Model 

```text
Kubernetes Cluster
│
├── Node
│     ├── Pod
│     │      ├── Container
│     │      └── Container
│     │
│     └── Pod
│            └── Container
│
└── Node
      └── Pod
              └── Container
```
