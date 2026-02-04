## RKE2 (Kubernetes v1.32) – Single-Node Install on Ubuntu 24.04

These steps install a single-node RKE2 server running Kubernetes v1.32.8 on Ubuntu 24.04. Uses **Flannel** CNI and **no NGINX Ingress**. Compatible with KubeEdge.

### 1. Prerequisites

- **OS**: Ubuntu Server 24.04 (64-bit)
- **Privileges**: User with `sudo` rights
- **Recommended minimum**: 2 CPU, 4 GB RAM

Update packages and install `curl`:

```bash
sudo apt-get update
sudo apt-get install -y curl
```

If you use `ufw`, either disable it or open required Kubernetes ports manually (not covered here):

```bash
sudo ufw disable || true
```

### 2. Configure and install RKE2 (Kubernetes v1.32, Flannel, no NGINX)

Create config before first start:

```bash
sudo mkdir -p /etc/rancher/rke2
sudo tee /etc/rancher/rke2/config.yaml << 'EOF'
cni: flannel
disable:
  - rke2-ingress-nginx
EOF
```

Install RKE2 pinned to `v1.32.8+rke2r1` (KubeEdge-compatible):

```bash
curl -sfL https://get.rke2.io | sudo INSTALL_RKE2_VERSION="v1.32.8+rke2r1" sh -
```

Enable and start the RKE2 server service:

```bash
sudo systemctl enable rke2-server.service
sudo systemctl start rke2-server.service
```

For single-node: remove control-plane taint so workloads can schedule:

```bash
kubectl taint nodes --all node-role.kubernetes.io/control-plane:NoSchedule-
```

Watch logs until the node becomes Ready and there are no errors:

```bash
sudo journalctl -u rke2-server -f
```

### 3. Configure kubectl access

RKE2’s kubeconfig is written to `/etc/rancher/rke2/rke2.yaml`. Copy it to your user and set permissions:

```bash
mkdir -p ~/.kube
sudo cp /etc/rancher/rke2/rke2.yaml ~/.kube/config
sudo chown $(id -u):$(id -g) ~/.kube/config
```

Add RKE2’s bundled `kubectl` to your `PATH`:

```bash
echo 'export PATH=/var/lib/rancher/rke2/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
```

### 4. Verify the cluster

Check Kubernetes versions:

```bash
kubectl version --short
```

You should see the server version as `v1.32.8` (or another v1.32.x in this line).

Check that the node is Ready:

```bash
kubectl get nodes
```

Check that core system pods are running:

```bash
kubectl get pods -A
```

At this point, your single-node RKE2 cluster on Ubuntu 24.04 is ready to use.

