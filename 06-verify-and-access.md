# Step 6 — Verify & Access the App

## 1. Confirm the Ingress got a public address

```bash
kubectl get ingress -n game-2048
```

Expected output:

```
NAME           CLASS   HOSTS   ADDRESS                                                          PORTS   AGE
ingress-2048   alb     *       k8s-game2048-ingress2-46ca8bb7f4-822590991.us-east-1.elb.amazonaws.com   80      29m
```

The value under `ADDRESS` **is your live public URL** — that's the DNS name of the ALB that the controller just created for you.

> 🔎 Common mixup: `kubectl get ingress -n kube-system` will correctly say **"No resources found"** — the Ingress object lives in the `game-2048` namespace (where the app is), not in `kube-system` (where the *controller* runs). Always check the app's own namespace.

## 2. Confirm the ALB exists in the AWS Console

Go to **EC2 Console → Load Balancers**. You should see a new **internet-facing** Application Load Balancer named something like `k8s-game2048-ingress2...`, with State `Active`.

## 3. Open it in your browser

Copy the `ADDRESS` value from step 1 into your browser:

```
http://k8s-game2048-ingress2-46ca8bb7f4-822590991.us-east-1.elb.amazonaws.com
```

You should see the playable **2048** game — score 0, best 0, a fresh board with a couple of `2` tiles. That confirms the full path is working:

**Internet → ALB → Target Group → Service → Pod**

## 4. (Optional) Check controller logs if something looks wrong

```bash
kubectl get deployment -n kube-system aws-load-balancer-controller
kubectl logs -n kube-system deployment/aws-load-balancer-controller
```

---
⬅️ [Deploy the 2048 App](05-deploy-app.md) | ➡️ Next: [Generic Sample App](07-generic-sample-app.md)
