## Local-path

```ShellSession
# Install
kubectl apply -f https://raw.githubusercontent.com/rancher/local-path-provisioner/master/deploy/local-path-storage.yaml
# Make default
kubectl patch storageclass local-path -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```

## Nfs

```ShellSession
# On all nodes!!!
sudo apt-get install -y nfs-common
# On node with kubeconfig
helm install nfs stable/nfs-server-provisioner
```
