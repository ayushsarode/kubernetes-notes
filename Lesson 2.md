# kubectl

kubectl never creates pods direclty
kubectl is just a client
**Its only job is to talk to the k8s API Server**

You
 │
 │ kubectl
 ▼
API Server
 │
 ▼
Kubernetes

The API Server is the **front door** of Kubernetes.
Every request goes through it.

**when we run kubectl get nodes**
`kubectl`
    `│`
`GET /api/v1/nodes`
    `│`
    `▼`
`API Server`
    `│`
`returns Nodes`
    `▼`
`kubectl prints them`

#### Note: K8s is an API, everything is stored as an object

### K8s resources

> A **resource** is any object that Kubernetes knows how to store, manage, and expose through its API.

Examples:

- Pod
- Deployment
- Service
- Secret
- ConfigMap
- Namespace
- Node
- Job
- CronJob

This is why `kubectl api-resources` lists them all.

### Why does Kubernetes expose everything as resources?

> **Kubernetes treats everything as an API object.**

Instead of inventing different commands for every feature, Kubernetes has one consistent model:

Resource
    │
CRUD Operations
(Create, Read, Update, Delete)****


# Note
## Here's something that will help later with OpenKruise

Remember when we ran:

```
kubectl api-resources
```

You saw a huge list.

Those aren't hardcoded into `kubectl`.

Projects can **add new resource types**.

For example, OpenKruise adds resources like `CloneSet`.

After installing OpenKruise, you could do:

```
kubectl get clonesets
```

even though Kubernetes didn't originally know about `CloneSet`.

How?
Because OpenKruise **extends the Kubernetes API** by adding new resource types (through CRDs).

This is one reason Kubernetes is so extensible.


## Complete flow

Who is responsible for making sure a CloneSet actually creates Pods?
ans: controller
kubectl apply
       │
       ▼
API Server
       │
Stores CloneSet
       ▼
OpenKruise Controller
       │
Creates Pods
       ▼
API Server
       │
Scheduler
       │
kubelet
       │
Running Pods

