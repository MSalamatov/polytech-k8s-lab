## Kubespray

Python:

```ShellSession
sudo apt-get update
sudo apt-get install python3-pip
```

Preparation:

```ShellSession
git clone https://github.com/kubernetes-sigs/kubespray.git
cd kubespray/
sudo pip3 install -r requirements.txt
cp -rfp inventory/sample inventory/mycluster
declare -a IPS=(192.168.116.230 192.168.116.238 192.168.116.243 192.168.116.215 192.168.116.207 192.168.116.228)
CONFIG_FILE=inventory/mycluster/hosts.yaml python3 contrib/inventory_builder/inventory.py ${IPS[@]}
```

Configuration:

```ShellSession
# Remove master-host from workers
sudo nano inventory/mycluster/hosts.yaml

# dashboard, helm, local_path_provisioner, cert_manager = true
sudo nano inventory/mycluster/group_vars/k8s-cluster/addons.yml

# Add floatting-ips in supplementary_addresses_in_ssl_keys
sudo nano inventory/mycluster/group_vars/k8s-cluster/k8s-cluster.yml
```

Go:

```ShellSession
ansible-playbook -i inventory/mycluster/hosts.yaml --become --become-user=root cluster.yml

# Useful options (add to previous command)
–-private-key=~/.ssh/k8s-test.key
--extra-vars "ansible_sudo_pass=passwordk8"
```

Kubectl:

```ShellSession
sudo apt-get update && sudo apt-get install -y apt-transport-https gnupg2
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubectl

mkdir ~/.kube
sudo cp /etc/kubernetes/admin.conf ~/.kube/config
sudo chmod +r ~/.kube/config
```

Helm:

```ShellSession
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh

helm repo add stable https://kubernetes-charts.storage.googleapis.com/
helm repo update
```
