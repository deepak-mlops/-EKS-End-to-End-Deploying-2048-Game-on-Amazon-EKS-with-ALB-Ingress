# Step 5 — Deploy the 2048 Sample App

Now that the cluster and the ALB Controller are ready, let's deploy something real to prove it all works: the classic 2048 puzzle game.

## 1. Create a Fargate profile for the app's namespace

Fargate profiles tell EKS "any pod in this namespace should run on Fargate, not on EC2 nodes." The 2048 app's manifests use the `game-2048` namespace, so:

```bash
eksctl create fargateprofile \
    --cluster demo-cluster \
    --region us-east-1 \
    --name alb-sample-app \
    --namespace game-2048
```

## 2. Deploy the Deployment + Service + Ingress in one shot

The maintainers of the ALB Controller publish a ready-made manifest bundling all three objects:

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/examples/2048/2048_full.yaml
```

This creates, in the `game-2048` namespace:
- A **Deployment** running the 2048 game container
- A **Service** (type `NodePort`) exposing those pods internally
- An **Ingress** (class `alb`) that tells the ALB Controller to create a public Application Load Balancer routing to that Service

## 3. Watch the pods come up

```bash
kubectl get pods -n game-2048
kubectl get pods -n game-2048 -w      # -w = watch, live updates
```

Wait until all pods show `STATUS: Running`.

## How it all connects

```
ingress-2048 (Ingress)  →  Load Balancer (ALB, created by the controller)  →  Target Group  →  Service (port 80)  →  Pods
```

---
⬅️ [Install ALB Controller](04-alb-controller.md) | ➡️ Next: [Verify & Access the App](06-verify-and-access.md)
