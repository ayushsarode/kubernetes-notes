# Lesson 9: Why Do Kubernetes Services Exist? 

> **If every Pod already has an IP address, why do we need a Service?**

The answer is simple:

**Pods are temporary.**

If a Pod crashes, Kubernetes creates a new one, and it gets a **new IP address**.

```text
Old Pod
10.0.0.5 ❌

New Pod
10.0.0.18 ✅
```

If applications connected directly to Pod IPs, they'd constantly break whenever Pods were recreated.

Kubernetes solves this by introducing a **Service**.

```text
         Service
    Stable IP Address
           │
     ┌─────┴─────┐
     │           │
+----------+ +----------+
| Pod A    | | Pod B    |
+----------+ +----------+
```

Applications communicate with the **Service**, not with individual Pods.

Even if a Pod is replaced, the Service IP stays the same.

### How does the Service know which Pods to send traffic to?

Using **labels and selectors**.

Pods:

```yaml
labels:
  app: backend
```

Service:

```yaml
selector:
  app: backend
```

Whenever a new Pod is created with the same label, Kubernetes automatically includes it behind the Service.

No manual updates required.

### Hands-on

Create a Deployment:

```bash
kubectl create deployment nginx --image=nginx
```

Expose it as a Service:

```bash
kubectl expose deployment nginx --port=80 --target-port=80
```

View Services:

```bash
kubectl get svc
```

Describe the Service:

```bash
kubectl describe svc nginx
```

Look for the **Selector** and **Endpoints** sections to see which Pods the Service is routing traffic to.

> **Pods are ephemeral. Services provide a stable entry point. Labels and selectors keep everything connected automatically.**