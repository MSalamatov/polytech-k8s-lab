## Local-path

Link: https://github.com/rancher/local-path-provisioner

```ShellSession
# Install
kubectl apply -f https://raw.githubusercontent.com/rancher/local-path-provisioner/master/deploy/local-path-storage.yaml
# Make default
kubectl patch storageclass local-path -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```

## Nfs

Link: https://github.com/helm/charts/tree/master/stable/nfs-server-provisioner

```ShellSession
# On all nodes!!!
sudo apt-get install -y nfs-common
# On node with kubeconfig
helm install nfs stable/nfs-server-provisioner
```
