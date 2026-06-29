# Inspecting a Deployment (Understanding the API Object)

> **What does a Deployment object actually look like inside the API Server?**

Remember:

kubectl
    │
    ▼
API Server
    │
stores
    ▼
Deployment Object

Everything in Kubernetes is just an API object stored in the API Server.

---
## View the Deployment YAML

Run:

```
kubectl get deployment nginx -o yaml
```

Think of them as:

```
metadata → Who am I?
spec → What do I want?
status → What is happening right now?
```

## Example

Suppose you create:

```
apiVersion: apps/v1kind: Deployment
metadata:  name: nginx
spec:  replicas: 3
status:  availableReplicas: 3
```

Interpret it in plain English:

- **metadata** → "My name is `nginx`."
- **spec** → "I want 3 replicas."
- **status** → "I currently have 3 running."

### Kubernetes is **declarative**, not imperative.

```
spec:  replicas: 5
```

Notice the wording.

You are **not giving commands**.

You're saying:

> "I always want five Pods."


# Why separate spec and status?

Suppose there were only one field:

```
replicas: 5
```

Now a Pod crashes.

Should Kubernetes change it to

```
replicas: 4
```

No.

That would mean

> "The user now wants 4 Pods."

Which is false.

The user's intent hasn't changed.

Instead Kubernetes separates:

```
Desired (spec)5 Pods
```

from

```
Reality (status)4 Pods
```


# The Reconciliation Loop

Every controller runs an infinite loop.

Think of it like this:

```
Loop forever↓Read desired state (spec)↓Read actual state (status)↓Compare↓If different↓Take action↓Repeat
```

This happens continuously.

Not once.

Forever.

---

## Example

User creates

```
spec:  replicas: 5
```

Initially

```
Desired = 5Actual = 0
```

ReplicaSet controller sees

```
5 != 0
```

Difference = 5 Pods

So it creates five Pods.

Now

```
Desired = 5Actual = 5
```

Nothing to do.

The controller simply keeps watching.

---

## A Pod crashes

Now reality changes.

```
Desired = 5Actual = 4
```

No human notices.

The ReplicaSet controller notices.

```
5 != 4
```

So it creates

```
Pod 5 (new)
```

Now

```
Desired = 5Actual = 5
```

The system is back in the desired state.

But here's the question:

> **How does the Deployment know which ReplicaSet belongs to it?**

And similarly:

> **How does the ReplicaSet know which Pods belong to it?**

Kubernetes **does not** use pointers or object references like you'd see in Java or Go.

Instead, it uses **Labels** and **Selectors**.

