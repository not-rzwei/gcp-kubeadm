# Bootstrap

## Control Plane

### Bootstrap

```bash
{
EXTERNAL_IP=$(curl -H "Metadata-Flavor: Google" http://metadata/computeMetadata/v1/instance/network-interfaces/0/access-configs/0/external-ip)
POD_CIDR=10.100.0.0/16
SERVICE_CIDR=10.200.0.0/24

sudo kubeadm init \
  --pod-network-cidr=$POD_CIDR \
  --service-cidr=$SERVICE_CIDR \
  --apiserver-cert-extra-sans=$EXTERNAL_IP \
  --ignore-preflight-errors=NumCPU
}
```

### Kubeconfig

```bash
{
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
}
```

```bash
{
EXTERNAL_IP=$(curl -H "Metadata-Flavor: Google" http://metadata/computeMetadata/v1/instance/network-interfaces/0/access-configs/0/external-ip)

cp $HOME/.kube/config $HOME/.kube/admin.config
kubectl config set-cluster kubernetes \
  --server=https://$EXTERNAL_IP:6443 \
  --kubeconfig $HOME/.kube/admin.config
}
```

### Pod Network

Calico

```bash
{
CALICO_POD_CIDR=192.168.0.0/16
POD_CIDR=10.100.0.0/16

curl -L https://docs.projectcalico.org/v3.11/manifests/calico.yaml |\
sed -e "s~${CALICO_POD_CIDR}~${POD_CIDR}~g" |\
kubectl apply -f -
}
```

Flannel

```bash
{
export FLANNEL_POD_CIDR=10.244.0.0/16
POD_CIDR=10.100.0.0/16

curl -L https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml |\
sed -e "s~${FLANNEL_POD_CIDR}~${POD_CIDR}~g" |\
kubectl apply -f -
}
```

### Token

```bash
kubeadm token create
```

```bash
kubeadm token list
```

## Worker

### Join

```bash
sudo kubeadm join 10.148.0.8:6443 --token gsbc8t.owgui1cactbx9khm \
    --discovery-token-ca-cert-hash sha256:33497e875996e3f68f3cdc4438242f2ceb9fc173a703a320c7fe73a637f018c1
```

### Token CA Hash

```bash
openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | \
  openssl rsa -pubin -outform der 2>/dev/null | \
  openssl dgst -sha256 -hex | sed 's/^.* //'
```