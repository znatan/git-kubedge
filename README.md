# KubeEdge Demo

Deploys an NGINX container on a KubeEdge edge node using RKE2, Flannel CNI, and Helm.

## Structure

| Path | Description |
|------|-------------|
| `kubeedge-demo-installation.md` | Full design and installation guide (cloud + edge) |
| `rke2-rke2.1.34-single-node-ubuntu-24.04.md` | RKE2 single-node cluster setup |
| `server-chart/` | Helm chart — deploys NGINX on the edge with `hostNetwork` |

## Quick Start

```bash
helm install server ./server-chart        # deploy
helm upgrade server ./server-chart        # upgrade
kubectl rollout restart deployment/server # restart
helm uninstall server                     # stop
```

Access NGINX directly on the edge node IP: `http://<edge-node-ip>:80`

## Known Issues

See [Section 11](kubeedge-demo-installation.md#11-known-issues-and-gotchas) in the installation guide for common gotchas (Flannel on edge, Mosquitto broker, `crictl` usage).
