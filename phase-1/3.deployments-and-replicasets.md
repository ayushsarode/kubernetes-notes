# Lesson 3: Deployments, ReplicaSets, and Controllers

## Creating a Deployment

Let's look at a common sequence of commands:
```bash
kubectl create deployment nginx --image=nginx
kubectl get deployments
kubectl get replicasets
kubectl get pods
```

Let's analyze what happened.

You executed: `kubectl create deployment nginx --image=nginx`

Did `kubectl` create a Pod?
**No.**

It sent a **Deployment API object** to the API Server. At that moment, the API Server stored something like:
```yaml
# Deployment
name: nginx
replicas: 1
image: nginx
```
Nothing more.

## Who Created the Pod?

The Deployment controller stops there. Now another controller is watching: The **ReplicaSet controller**.

It saw:
```text
Desired replicas = 1
Current Pods = 0
```

So it created a Pod.
```text
ReplicaSet
   │
ReplicaSet Controller
   │ creates
   ▼
Pod
```

Notice something important: **The Deployment never creates Pods directly.**

Instead:
```text
Deployment
   ↓
Deployment Controller
   ↓
ReplicaSet
   ↓
ReplicaSet Controller
   ↓
Pod
```

This chain of controllers is a core Kubernetes design pattern.

## The Single Responsibility Principle

**Why doesn't the Deployment controller create Pods directly? Why do you think Kubernetes inserts a ReplicaSet in between?**

Because Kubernetes follows the **Single Responsibility Principle**. Each controller has **one job**.

```text
Deployment Controller
       └── Manages ReplicaSets

ReplicaSet Controller
       └── Manages Pods
```

If the Deployment controller also managed Pods directly, it would have to implement all the logic for:
- Creating Pods
- Deleting Pods
- Replacing failed Pods
- Scaling Pods
- Tracking Pod health

But Kubernetes already has a controller that knows how to do all of that: the **ReplicaSet controller**.

So instead of duplicating that logic, the Deployment controller simply says:
> "I need a ReplicaSet with 3 replicas."

Then the ReplicaSet controller handles the Pods.

## The Complete Chain

```text
kubectl
   │
   ▼
API Server
   │
   ▼
Deployment (replicas = 5)
   │
   ▼
Deployment Controller
   │ updates
   ▼
ReplicaSet (replicas = 5)
   │
   ▼
ReplicaSet Controller
   │ creates
   ▼
5 Pod objects
   │
   ▼
Scheduler + kubelet
   │
   ▼
Running containers
```
