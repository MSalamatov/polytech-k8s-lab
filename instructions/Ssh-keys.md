## SSH

Add access with openstack key.

On your PC:

```ShellSession
# Problem with rules
sudo chmod 600 ~/.ssh/k8s-test.key
# Connect to remote node
ssh -i ~/.ssh/k8s-test.key ubuntu@192.168.116.238
# Send ssh key to remote node
scp -i ~/.ssh/k8s-test.key ~/.ssh/k8s-test.key ubuntu@195.208.116.250:~/.ssh/k8s-test.key
```


TODO 
Create new key (if not existed):

```ShellSession
cp ~/.ssh
ssh-keygen -t rsa -f server.key
ssh-copy-id ...
```
