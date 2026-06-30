# Lesson 2: Understanding kubectl and the Kubernetes API

## What is `kubectl`?

`kubectl` never creates pods directly. It is just a client.
**Its only job is to talk to the Kubernetes API Server.**

```text
You
 │
 │ kubectl
 ▼
API Server
 │
 ▼
Kubernetes
```

The API Server is the **front door** of Kubernetes. Every request goes through it.

**When we run `kubectl get nodes`:**
```text
kubectl
   │ GET /api/v1/nodes
   ▼
API Server
   │ returns Nodes
   ▼
kubectl prints them
```

## Kubernetes Resources

**Note:** Kubernetes is an API, and everything is stored as an object.

> A **resource** is any object that Kubernetes knows how to store, manage, and expose through its API.

Examples of resources:
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

```text
Resource
    │
CRUD Operations
(Create, Read, Update, Delete)
```

## Extensibility (Advanced Note)


Remember when we ran `kubectl api-resources`? You saw a huge list. Those aren't hardcoded into `kubectl`.

Projects can **add new resource types**.

For example, OpenKruise adds resources like `CloneSet`. After installing OpenKruise, you could do:
```bash
kubectl get clonesets
```
even though Kubernetes didn't originally know about `CloneSet`.

**How?** Because OpenKruise **extends the Kubernetes API** by adding new resource types (through CRDs - Custom Resource Definitions). This is one reason Kubernetes is so extensible.

## The Complete Flow

Who is responsible for making sure a CloneSet actually creates Pods? 
**Answer: The Controller**

```text
kubectl apply
       │
       ▼
API Server
       │ Stores CloneSet
       ▼
OpenKruise Controller
       │ Creates Pods
       ▼
API Server
       │
Scheduler
       │
kubelet
       │
Running Pods
```
