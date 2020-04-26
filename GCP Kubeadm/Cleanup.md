# Cleanup

## Cluster

### Reset kubeadm cluster

```bash
sudo kubeadm reset --force
```

### Remove kubeconfig

```bash
rm -rf ~/.kube
```

### Remove CNI plugin config

```bash
sudo rm -rf /etc/cni/net.d/
```

### Reset iptables

```bash
{
sudo iptables -F
sudo iptables -t nat -F
sudo iptables -t mangle -F
sudo iptables -X
}
```

## GCP

### Delete instance

```bash
{
INSTANCES=$(gcloud compute instances list --filter=tags.items=kubeadm --format='value(name)')
gcloud compute instances delete $INSTANCES --zone asia-southeast1-a -q
}
```

### Delete firewall rules

```bash
gcloud compute firewall-rules delete kubeadm-allow-apiserver -q
```