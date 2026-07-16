# Step 3 — Configure the IAM OIDC Provider

## Why is this needed?

The AWS Load Balancer Controller (which we install next) needs permission to create real AWS resources — Application Load Balancers, Target Groups, Security Group rules, etc. Instead of giving broad permissions to the whole cluster, EKS uses **IRSA (IAM Roles for Service Accounts)**: a specific Kubernetes ServiceAccount is bound to a specific IAM Role with only the permissions it needs.

For IRSA to work, your cluster needs to be registered as an **OIDC identity provider** in IAM. You only need to do this once per cluster.

## Commands

```bash
export cluster_name=demo-cluster

oidc_id=$(aws eks describe-cluster --name $cluster_name \
  --query "cluster.identity.oidc.issuer" --output text | cut -d '/' -f 5)
```

### Check if an OIDC provider is already configured

```bash
aws iam list-open-id-connect-providers | grep $oidc_id
```

### If nothing was found, create it

```bash
eksctl utils associate-iam-oidc-provider --cluster $cluster_name --approve
```

That's it — your cluster can now hand out fine-grained IAM permissions to individual Kubernetes ServiceAccounts.

---
⬅️ [Create the EKS Cluster](02-cluster-setup.md) | ➡️ Next: [Install the ALB Controller](04-alb-controller.md)
