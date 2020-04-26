# Remote Access

## Kubeconfig

```bash
gcloud compute scp kubeadm-master:~/.kube/admin.config .
```

## Kubectl

```bash
KUBECONFIG=admin.config
kubectl get node
```