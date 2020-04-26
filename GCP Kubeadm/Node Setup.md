# Node Setup

## Kubernetes Package

```bash
{
VERSION=1.18.0-00
sudo apt-get update && sudo apt-get install -y apt-transport-https curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF
sudo apt-get update
sudo apt-get install -y kubelet=$VERSION kubeadm=$VERSION kubectl=$VERSION
sudo apt-mark hold kubelet kubeadm kubectl
}
```

## Docker

### Installation

```bash
{
sudo apt update
sudo apt install -y docker.io
sudo gpasswd -a $(whoami) docker
}
```

### Docker setting

```bash
cat <<EOF | sudo tee /etc/docker/daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF
```

### Enable Docker

```bash
{
sudo systemctl daemon-reload
sudo systemctl enable docker
sudo systemctl restart docker
}
```

## Networking

### Package

```bash
sudo apt install -y ebtables ethtool conntrack socat
```

### Setting

```bash
{
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF
sudo sysctl --system
}
```