## Basic Imp things

#### Cluster -> Node(s) -> Pods
everything we deploy eventually becomes one or more pods

For example:
A Deployment creates Pods.
OpenKruise manages Pods.
KubeArmor secures Pods.
Dapr injects a sidecar into Pods.
Kube-burner creates thousands of Pods.

we learnt
`kind create cluster --name learning   # Skip if you already have one`
`kubectl cluster-info`
`kubectl get nodes`
`kubectl get namespaces`
`kubectl get pods -A`
`kubectl describe node`

#### Cluster
Entire k8s environment.
It contains:

- One or more nodes
- Networking
- The Kubernetes API
- Everything needed to run applications

#### Node
simply a **machine** (physical or virtual)
Its job is to run Pods

Every node has:
- CPU
- Memory
- Storage
- A container runtime
- A `kubelet`

#### Pod
A pod is  the smallest deployable unit in k8s

A Pod usually contains:

- One application container
- Sometimes helper containers (sidecars)

Examples:

```
Pod └── nginx container
```

or

```
Pod ├── Go API └── Dapr sidecar
```


### Note
## The one thing I'd add

Pods **don't exist by themselves** in real applications.

Usually you create a **Deployment**, and the Deployment creates Pods for you.

```
Deployment     ->     ReplicaSet      ->      Pods   ->    Containers
```

You'll almost never run:

```
kubectl run nginx
```

Instead, you'll create a **Deployment** because it ensures the desired number of Pods are always running.



## Mental Model 

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


