# Step 1 — Prerequisites

Before touching AWS, install these four tools locally.

## 1. AWS CLI

The command-line tool used to talk to every AWS service.

- Install: https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html
- Configure it with an IAM user's credentials:
  ```bash
  aws configure
  ```
  You'll be asked for:
  - AWS Access Key ID
  - AWS Secret Access Key
  - Default region (e.g. `us-east-1`)
  - Default output format (e.g. `json`)

## 2. kubectl

The command-line tool used to talk to any Kubernetes cluster (not just EKS).

- Install: https://kubernetes.io/docs/tasks/tools/install-kubectl/

## 3. eksctl

A CLI built specifically for EKS. It wraps a lot of manual CloudFormation/IAM work into single commands like `eksctl create cluster`.

```bash
# for ARM systems, set ARCH to: arm64, armv6 or armv7
ARCH=amd64
PLATFORM=$(uname -s)_$ARCH
curl -sLO "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_$PLATFORM.tar.gz"

# (Optional) Verify checksum
curl -sL "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_checksums.txt" | grep $PLATFORM | sha256sum --check

tar -xzf eksctl_$PLATFORM.tar.gz -C /tmp && rm eksctl_$PLATFORM.tar.gz
sudo install -m 0755 /tmp/eksctl /usr/local/bin && rm /tmp/eksctl

eksctl version
```

## 4. Helm

The package manager for Kubernetes — used later to install the AWS Load Balancer Controller.

- Install: https://helm.sh/docs/intro/install/

## Optional: Practice locally first with Minikube

If you want to test your Kubernetes manifests (Deployments, Services) locally before spending money on AWS, spin up Minikube:

```bash
minikube start --driver=docker
```

This gives you a local single-node cluster to experiment with `kubectl apply` before doing it "for real" on EKS. Nothing you do on Minikube touches AWS or costs money — it's purely for learning/testing.

---
⬅️ [Back to README](../README.md) | ➡️ Next: [Create the EKS Cluster](02-cluster-setup.md)
