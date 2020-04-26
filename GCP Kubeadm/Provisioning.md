# Provisioning

## Setup

### Google Cloud SDK

```bash
curl https://sdk.cloud.google.com | bash
```

### Authenticate

```bash
gcloud auth
```

## VM

### Master

```bash
gcloud compute instances create kubeadm-master \
    --async \
    --boot-disk-size 200GB \
    --can-ip-forward \
    --image-family ubuntu-1604-lts \
    --image-project ubuntu-os-cloud \
    --machine-type n1-standard-1 \
    --zone asia-southeast1-a \
    --scopes compute-rw,storage-ro,service-management,service-control,logging-write,monitoring \
    --subnet default \
    --tags kubeadm \
    --labels kubeadm-role=master
```

### Worker

```bash
for i in 1; do
gcloud compute instances create kubeadm-worker-${i} \
    --async \
    --boot-disk-size 200GB \
    --can-ip-forward \
    --image-family ubuntu-1604-lts \
    --image-project ubuntu-os-cloud \
    --machine-type n1-standard-1 \
    --zone asia-southeast1-a \
    --scopes compute-rw,storage-ro,service-management,service-control,logging-write,monitoring \
    --subnet default \
    --tags kubeadm \
    --labels kubeadm-role=worker
done
```

## Firewall

```bash
gcloud compute firewall-rules create kubeadm-allow-apiserver \
  --direction=INGRESS \
  --network=default \
  --action=ALLOW \
  --rules=tcp:6443 \
  --source-ranges=0.0.0.0/0 \
  --target-tags=kubeadm
```