# Step 8 — Clean Up (Don't Skip This!)

EKS clusters and the Application Load Balancers they create **bill by the hour**, whether you're using them or not. Always tear everything down when you're done experimenting.

## The right order matters

If you delete the cluster *before* removing the Ingress/namespace, the ALB Controller pods disappear along with the cluster — and can no longer clean up the ALB, target groups, and security groups they created. This can leave orphaned AWS resources quietly costing you money.

**Recommended order:**

### 1. Delete the app namespace (this removes the Ingress, which tells the controller to delete the ALB)

```bash
kubectl delete namespace game-2048
```

You can confirm the ALB is gone in the EC2 Console, or via:

```bash
aws elbv2 describe-load-balancers --region us-east-1
```

### 2. Delete the EKS cluster

```bash
eksctl delete cluster --name demo-cluster --region us-east-1
```

`eksctl` will automatically:
- Delete any Fargate profiles
- Clean up any leftover Load Balancers created by Kubernetes Services/Ingresses/Gateways
- Delete IAM roles/ServiceAccounts created via IRSA (e.g. `aws-load-balancer-controller`)
- Delete the IAM OIDC provider
- Delete the cluster control plane itself

Example of what a clean deletion looks like in the terminal:

```
deleting EKS cluster "demo-cluster1"
deleting Fargate profile "alb-sample-app"
deleted Fargate profile "alb-sample-app"
deleted 1 Fargate profile(s)
kubeconfig has been updated
cleaning up AWS load balancers created by Kubernetes objects of Kind Service, Ingress, or Gateway
2 sequential tasks: {
    2 sequential sub-tasks: {
        delete IAM role for serviceaccount "kube-system/aws-load-balancer-controller",
        delete serviceaccount "kube-system/aws-load-balancer-controller",
    },
    delete IAM OIDC provider,
}, delete cluster control plane "demo-cluster1" [async]
...
all cluster resources were deleted
```

## ⚠️ Use the *exact* cluster name you created

A very easy mistake: if you created `demo-cluster1` (with the "1") but try to delete `demo-cluster` (without it), you'll get:

```
Error: unable to describe cluster control plane: operation error EKS: DescribeCluster,
https response error StatusCode: 404, ResourceNotFoundException: No cluster found for name: demo-cluster.
```

Always double check with:

```bash
aws eks list-clusters --region us-east-1
```

...and delete each cluster name that actually exists. If you accidentally end up with multiple clusters (e.g. both `demo-cluster` and `demo-cluster1` from re-running commands), delete them all — leaving an unused cluster running is one of the most common sources of surprise AWS bills.

## Final check

```bash
aws eks list-clusters --region us-east-1
aws elbv2 describe-load-balancers --region us-east-1
```

Both should come back empty (no clusters, no load balancers) once you're fully cleaned up.

---
⬅️ [Generic Sample App](07-generic-sample-app.md) | ➡️ Next: [Troubleshooting](09-troubleshooting.md)
