# Step 9 — Troubleshooting

Real issues you're likely to hit (and how to fix them), based on common mistakes when running this project.

## ❌ `No cluster found for name: demo-cluster`

**Cause:** You typed the wrong cluster name into a command — for example the cluster is actually named `demo-cluster1`, but you ran `eksctl delete cluster --name demo-cluster`.

**Fix:** List your real clusters first, then use the exact name that comes back:

```bash
aws eks list-clusters --region us-east-1
eksctl delete cluster --name <exact-name-from-above> --region us-east-1
```

## ❌ ALB Controller deployment stuck at `1/2` Ready

Right after `helm install`, it's completely normal to see:

```
NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
aws-load-balancer-controller   1/2     2            1           59s
```

**Fix:** Just wait — the second replica needs ~1–2 minutes to become ready. Confirm with:

```bash
watch kubectl get deployment -n kube-system aws-load-balancer-controller
```

It should settle at `2/2`.

## ❌ `kubectl get ingress -n kube-system` shows "No resources found"

**This is expected, not an error.** The ALB *controller* runs in `kube-system`, but the Ingress *object* for your app lives in the app's own namespace.

**Fix:** Check the app's namespace instead:

```bash
kubectl get ingress -n game-2048
```

## ❌ Ingress has no `ADDRESS` after several minutes

**Possible causes & fixes:**
- The ALB Controller isn't running yet — check `kubectl get deployment -n kube-system aws-load-balancer-controller`.
- The IAM policy/role attached to the controller's ServiceAccount is missing permissions — re-check Step 4.
- The subnets in your VPC aren't tagged correctly for ALB discovery (`kubernetes.io/role/elb=1` for public subnets). This is usually only an issue on custom/non-`eksctl`-created VPCs.

## ❌ Old ALB / target groups still exist after deleting the cluster

**Cause:** The cluster (and therefore the ALB Controller) was deleted *before* the Ingress/namespace was removed, so nothing ever told AWS to clean up the load balancer.

**Fix:**
```bash
aws elbv2 describe-load-balancers --region us-east-1
aws elbv2 delete-load-balancer --load-balancer-arn <arn-from-above>
```
Then double check for any leftover target groups and security groups the ALB created, and delete those too.

**Prevention:** Always follow the [cleanup order](08-cleanup.md): delete the namespace/Ingress **first**, then delete the cluster.

## ❌ Multiple clusters end up existing at once (e.g. `demo-cluster` *and* `demo-cluster1`)

This usually happens from re-running `eksctl create cluster` with a slightly different name while debugging. Each one bills separately.

**Fix:** List everything and delete what you don't need:

```bash
aws eks list-clusters --region us-east-1
eksctl delete cluster --name demo-cluster --region us-east-1
eksctl delete cluster --name demo-cluster1 --region us-east-1
```

## ❌ `eksctl create iamserviceaccount` fails with a policy ARN error

**Cause:** You left the placeholder `<your-aws-account-id>` or `<your-cluster-name>` in the command instead of substituting your real values.

**Fix:** Get your account ID and substitute it:

```bash
aws sts get-caller-identity --query Account --output text
```

---
⬅️ [Back to Cleanup](08-cleanup.md) | 🏠 [Back to README](../README.md)
