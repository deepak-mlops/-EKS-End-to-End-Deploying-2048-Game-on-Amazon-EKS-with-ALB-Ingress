# Step 4 — Install the AWS Load Balancer Controller

This controller watches for Kubernetes `Ingress` and `Service` objects in your cluster and automatically provisions real AWS Application Load Balancers (ALB) or Network Load Balancers (NLB) to match them. Without it, an `Ingress` object is just inert YAML — nothing routes traffic to it.

## 1. Download the IAM policy the controller needs

```bash
curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.11.0/docs/install/iam_policy.json
```

## 2. Create the IAM policy in your AWS account

```bash
aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy.json
```

## 3. Create an IAM Role + Kubernetes ServiceAccount (IRSA)

Replace `<your-cluster-name>` and `<your-aws-account-id>` with your real values.

```bash
eksctl create iamserviceaccount \
  --cluster=<your-cluster-name> \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --role-name AmazonEKSLoadBalancerControllerRole \
  --attach-policy-arn=arn:aws:iam::<your-aws-account-id>:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve
```

> 💡 Find your account ID with `aws sts get-caller-identity --query Account --output text`.

## 4. Add the EKS Helm repo

```bash
helm repo add eks https://aws.github.io/eks-charts
helm repo update eks
```

## 5. Install the controller

Replace `<your-cluster-name>`, `<your-region>`, and `<your-vpc-id>`.

```bash
helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system \
  --set clusterName=<your-cluster-name> \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller \
  --set region=<your-region> \
  --set vpcId=<your-vpc-id>
```

> 💡 Find your VPC ID in the EKS console under **Networking**, or with:
> ```bash
> aws eks describe-cluster --name <your-cluster-name> --query "cluster.resourcesVpcConfig.vpcId" --output text
> ```

## 6. Verify the controller is running

```bash
kubectl get deployment -n kube-system aws-load-balancer-controller
```

Expected output (it runs 2 replicas for high availability):

```
NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
aws-load-balancer-controller   2/2     2            2           2m2s
```

> 🕒 It's normal to briefly see `1/2` right after install — give it a minute and check again:
> ```bash
> watch kubectl get deployment -n kube-system aws-load-balancer-controller
> ```

---
⬅️ [Configure OIDC](03-oidc-setup.md) | ➡️ Next: [Deploy the 2048 App](05-deploy-app.md)
