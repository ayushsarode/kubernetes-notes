`kubectl create deployment nginx --image=nginx`
`kubectl get deployments`
`kubectl get replicasets`
`kubectl get pods`


Let's analyze what happened.

```
You executed:kubectl create deployment nginx --image=nginx
```

Did `kubectl` create a Pod?

**No.**

It sent a **Deployment API object** to the API Server.

At that moment, the API Server stored something like:

```
Deploymentname: nginxreplicas: 1image: nginx
```

Nothing more.


## Then who created the Pod?

The Deployment controller stops there.

Now another controller is watching.

Which one?

The **ReplicaSet controller**.

It saw:

```
Desired replicas = 1Current Pods = 0
```

So it created a Pod.

```
ReplicaSet      │ReplicaSet Controller      │creates      ▼Pod
```

Notice something important:

**The Deployment never creates Pods directly.**

Instead:

```
Deployment        ↓Deployment Controller        ↓ReplicaSet        ↓ReplicaSet Controller        ↓Pod
```

This chain of controllers is a core Kubernetes design pattern.


## Why doesn't the Deployment controller create Pods directly? Why do you think Kubernetes inserts a ReplicaSet in between?

### Kubernetes follows the Single Responsibility Principle

Each controller has **one job**.

```
Deployment Controller        │        └── Manages ReplicaSetsReplicaSet Controller        │        └── Manages Pods
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

## The complete chain

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
   │
updates
   ▼
ReplicaSet (replicas = 5)
   │
   ▼
ReplicaSet Controller
   │
creates
   ▼
5 Pod objects
   │
   ▼
Scheduler + kubelet
   │
   ▼
Running containers

