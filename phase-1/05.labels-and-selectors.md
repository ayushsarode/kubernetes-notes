# Lesson 5: Labels & Selectors 🏷️

Today I learned why Kubernetes uses **labels** instead of tracking Pod IDs.

You can even see the labels yourself:

kubectl get pods --show-labels

```Example output:

NAME                    READY   STATUS    LABELS
nginx-7f8fbb96d-x6cb2   1/1     Running   app=nginx,pod-template-hash=7f8fbb96d
```

The app=nginx label is what the Deployment uses to find and manage its Pods.

Imagine these Pods:

```text
Pod A → app=nginx
Pod B → app=nginx
Pod C → app=redis
```

A Deployment that wants to manage the **nginx** Pods doesn't store their names or UIDs. Instead, it uses a **selector**:

```text
Selector: app=nginx
```

So the relationship looks like this:

```text
Deployment
     │
     │ Selector: app=nginx
     ▼
ReplicaSet
     │
     ▼
Pods
 ├── app=nginx ✅
 ├── app=nginx ✅
 └── app=redis ❌
```

This design is powerful because Pods are **ephemeral**. If one Pod crashes, a new Pod gets a different name and UID, but it keeps the same label.

```text
Old Pod ❌
app=nginx

      ↓

New Pod ✅
app=nginx
```

The Deployment doesn't care which Pod it is—it simply asks:

> "Show me every Pod with `app=nginx`."

That's why Kubernetes relies on **labels and selectors** instead of fixed object IDs.

**Labels identify resources. Selectors find them.**
