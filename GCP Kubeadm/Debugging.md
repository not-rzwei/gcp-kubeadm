# Debugging

## Set default namespace

```bash
kubectl config set-context --current --namespace kube-system
```

## List kubernetes components

```bash
kubectl get pods -l tier=control-plane
kubectl get pods -l tier=node
```

## Print component logs

```bash
kubectl logs -l component=etcd
kubectl logs -l component=kube-apiserver
kubectl logs -l component=kube-controller-manager
kubectl logs -l component=kube-scheduler
```