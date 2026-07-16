# Step 2 — Create the EKS Cluster

## Why Fargate?

Normally, a Kubernetes cluster needs **worker nodes** (EC2 instances) that you provision, patch, and scale yourself. With **AWS Fargate**, EKS runs your pods on serverless compute — no EC2 instances to manage at all. This is the fastest way to get a real EKS cluster running for learning purposes.

## Create the cluster

```bash
eksctl create cluster --name demo-cluster --region us-east-1 --fargate
```

This single command does a *lot* behind the scenes:
- Creates a VPC, subnets, and route tables (unless you point it at an existing VPC)
- Creates the EKS control plane
- Creates a default Fargate profile for the `default` and `kube-system` namespaces
- Updates your local `~/.kube/config` so `kubectl` immediately works

⏱️ This takes **15–20 minutes**. Grab a coffee.

> ⚠️ **Pick one cluster name and stick with it for the whole project.** A very common mistake (see [Troubleshooting](09-troubleshooting.md)) is creating `demo-cluster1` but later running commands against `demo-cluster` — every `eksctl`/`kubectl` command after this point must use the **exact same cluster name**.

## Point kubectl at the new cluster

If for any reason your kubeconfig isn't already updated (e.g. you're on a different terminal/machine):

```bash
aws eks update-kubeconfig --name demo-cluster --region us-east-1
```

## Verify

```bash
kubectl get svc
```

You should see the default `kubernetes` service — confirming `kubectl` can talk to your new EKS cluster.

---
⬅️ [Prerequisites](01-prerequisites.md) | ➡️ Next: [Configure the IAM OIDC Provider](03-oidc-setup.md)
