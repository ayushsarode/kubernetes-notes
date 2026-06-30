# Lesson 4: API Objects, Desired State, and the Reconciliation Loop

## Inspecting a Deployment (Understanding the API Object)

> **What does a Deployment object actually look like inside the API Server?**

Remember:
```text
kubectl
    │
    ▼
API Server
    │ stores
    ▼
Deployment Object
```
Everything in Kubernetes is just an API object stored in the API Server.

## Viewing the Deployment YAML

Run the following command to see the raw object:
```bash
kubectl get deployment nginx -o yaml
```

Think of the structure as:
- **`metadata`** → Who am I?
- **`spec`** → What do I want?
- **`status`** → What is happening right now?

### Example

Suppose you create:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
status:
  availableReplicas: 3
```

Interpret it in plain English:
- **`metadata`** → "My name is `nginx`."
- **`spec`** → "I want 3 replicas."
- **`status`** → "I currently have 3 running."

## Declarative vs. Imperative

Kubernetes is **declarative**, not imperative.

When you specify:
```yaml
spec:
  replicas: 5
```
Notice the wording. You are **not giving commands**. You're saying:
> "I always want five Pods."

### Why Separate Spec and Status?

Suppose there were only one field: `replicas: 5`

Now a Pod crashes. Should Kubernetes change it to `replicas: 4`?
**No.**

That would mean:
> "The user now wants 4 Pods."

Which is false. The user's intent hasn't changed. Instead, Kubernetes separates them:
```text
Desired (spec)  = 5 Pods
Reality (status) = 4 Pods
```

## The Reconciliation Loop

Every controller runs an infinite loop. Think of it like this:

```text
Loop forever
   ↓
Read desired state (spec)
   ↓
Read actual state (status)
   ↓
Compare
   ↓
If different
   ↓
Take action
   ↓
Repeat
```
This happens continuously. Not once. Forever.

### Example: Scaling Up

User creates:
```yaml
spec:
  replicas: 5
```

Initially:
```text
Desired = 5
Actual  = 0
```

ReplicaSet controller sees: `5 != 0`.
Difference = 5 Pods. So it creates five Pods.

Now:
```text
Desired = 5
Actual  = 5
```
Nothing to do. The controller simply keeps watching.

### Example: A Pod Crashes

Now reality changes (a Pod crashes).
```text
Desired = 5
Actual  = 4
```

No human notices. The ReplicaSet controller notices: `5 != 4`.
So it creates a new Pod.

Now:
```text
Desired = 5
Actual  = 5
```
The system is back in the desired state.

## The Next Question: How do they find each other?

**Question:** If the Deployment, ReplicaSet, and Pods are all separate objects, how do they know they belong together? For example, how does a ReplicaSet know exactly which Pods it's supposed to manage?

**Answer:** They use name tags! 

Unlike traditional programming where objects have strict links or pointers to one another, Kubernetes uses a much more flexible system called **Labels and Selectors**.

- A Pod gets a **Label** (think of it as a sticky name tag, like `app=nginx`).
- The ReplicaSet uses a **Selector** (a rule saying, "I am responsible for managing *any* Pod wearing the `app=nginx` name tag").
