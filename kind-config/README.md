## Local Kubernetes Cluster with KIND

Spin up a multi-node Kubernetes cluster locally using KIND (Kubernetes IN Docker) no cloud provider required.

### Prerequisites

Docker installed and running (Docker Desktop with WSL2 integration, or Docker Engine). `kubectl` installed.

### 1. Install kubectl

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
kubectl version --client
```

### 2. Install KIND

```bash
curl -Lo ./kind https://kind.sigs.k8s.io/dl/latest/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind
kind version
```

### 3. Cluster config

`kind-config/kind-config.yaml` defines a 3-node cluster: 1 control-plane, 2 workers.

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
  - role: control-plane
  - role: worker
  - role: worker
```

### 4. Create the cluster

```bash
kind create cluster --config kind-config/kind-config.yaml --name k8s-demo
```

Verify:

```bash
kubectl cluster-info --context kind-k8s-demo
kubectl get nodes
```

Expected output, all nodes `Ready`:

```
NAME                     STATUS   ROLES           AGE   VERSION
k8s-demo-control-plane   Ready    control-plane   ...   v1.36.1
k8s-demo-worker          Ready    <none>          ...   v1.36.1
k8s-demo-worker2         Ready    <none>          ...   v1.36.1
```

### 5. Test deployment nginx

```bash
kubectl create deployment nginx --image=nginx
kubectl expose deployment nginx --port=80 --type=NodePort
kubectl get all
```

Access it locally via port-forward:

```bash
kubectl port-forward deployment/nginx 8080:80
```

Then open `http://localhost:8080`.

### Cleanup

```bash
kind delete cluster --name k8s-demo
```
