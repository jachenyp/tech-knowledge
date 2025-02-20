### How to make kind create cluster can be accessed remotly


1. add extraSANs in cluster config:
```yaml
# kind-config.yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
networking:
  apiServerAddress: "0.0.0.0"  # 让 API Server 监听所有地址
  apiServerPort: 6443
nodes:
- role: control-plane
  extraPortMappings:
    - containerPort: 6443
      hostPort: 6443
    # 允许远程访问
  kubeadmConfigPatches:
    - |
      apiVersion: kubeadm.k8s.io/v1beta3
      kind: ClusterConfiguration
      apiServer:
        certSANs:
          - "127.0.0.1"
          - "localhost"
          - "10.96.0.1"
          - "10.225.9.195"  # 添加你的远程 IP
          - "kind-control-plane"
```

2. get the control-pane api certificate  with command, and copy it to your remote kubeconfig file:
```shell
docker cp kas-control-plane:/etc/kubernetes/pki/ca.crt .
```
3. get and copy the client authorization data to your remote kubeconfig file
```
# get config
cat ~/.kube/config

# find the users(clients) data and copy it to your remote kubeconfig file