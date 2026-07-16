# Step 7 (Optional) — Deploy a Generic Sample App

Want to practice with your own plain Deployment + Service, separate from the 2048 example? Use the manifests in [`manifests/`](../manifests) — a simple Nginx app.

## 1. Deploy it

```bash
kubectl apply -f manifests/deploy.yaml
kubectl apply -f manifests/service.yaml
```

## 2. What's in these files

**`deploy.yaml`** — a `Deployment` running 3 replicas of Nginx, scheduled only on Linux nodes that are `amd64` or `arm64`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: eks-sample-linux-deployment
  labels:
    app: eks-sample-linux-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: eks-sample-linux-app
  template:
    metadata:
      labels:
        app: eks-sample-linux-app
    spec:
      containers:
      - name: nginx
        image: public.ecr.aws/nginx/nginx:1.23
        ports:
        - name: http
          containerPort: 80
      nodeSelector:
        kubernetes.io/os: linux
```

**`service.yaml`** — a `ClusterIP` Service that load-balances traffic across those 3 pods internally:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: eks-sample-linux-service
  labels:
    app: eks-sample-linux-app
spec:
  selector:
    app: eks-sample-linux-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

## 3. Verify

```bash
kubectl get deployment eks-sample-linux-deployment
kubectl get pods -l app=eks-sample-linux-app
kubectl get svc eks-sample-linux-service
```

> Note: this Service is `ClusterIP` (internal-only) — it won't get a public address on its own. To expose it to the internet the way the 2048 app is exposed, you'd add an `Ingress` object pointing at it (same pattern as Step 5).

---
⬅️ [Verify & Access](06-verify-and-access.md) | ➡️ Next: [Clean Up](08-cleanup.md)
