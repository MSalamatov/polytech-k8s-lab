## Network recovery

For Openstack VMs. Execute on all nodes:

```ShellSession
sudo systemctl disable systemd-resolved.service
sudo systemctl stop systemd-resolved.service
sudo rm /etc/resolv.conf
sudo sed -i "2 i\127.0.0.1 $HOSTNAME localhost localhost.localdomain" /etc/hosts
sudo touch /etc/resolv.conf
sudo chmod +w /etc/resolv.conf
echo 'nameserver 8.8.8.8' | sudo tee /etc/resolv.conf
```

Check:

```ShellSession
cat /etc/resolv.conf
ping google.com
```
