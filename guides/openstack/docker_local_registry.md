## Creating Docker Local Registry

Docker Local Registry is an ordinary container upped on our node with registry image.

###   1. Generating a certificate

After connecting to node the first thing we need to do is to install OpenSSL utils. After that we need to edit configuration file.

```ShellSession
sudo nano /etc/ssl/openssl.cnf
```
or
```ShellSession
sudo vim /etc/ssl/openssl.cnf
```
We need to add a row in section [v3_ca]
```ShellSession
[v3_ca]
...
subjectAltName = IP:<Node IP>
...
```

We can also safe remove row in the beginning
```ShellSession
RANDFILE = $ENV::HOME/.rnd
```

Now we can generate certificate
```ShellSession
sudo mkdir /certs
cd /certs
sudo openssl req -newkey rsa:4096 -x509 -sha256 -nodes -days 365 -out /certs/domain.crt -keyout /certs/domain.key 
```
 * req - generating request to sign certificate
 * -x509 - self-signed certificate
 * -newkey - create a new key
 *  rsa:4096 - generate a 4096-bit RSA key
 *  -nodes - no DES, don't encrypt the private key
 * -days 365 - number of days for the cerificate being valid
 * -keyout - we generate the key so we specify an output path
 * -out - path for our certificate
 
We need to give some info about our organization to fill the certificate.

### 2. Installing a certificate
After that we will have domain.crt and domain.key in /certs
We can install certificate by adding it to ca-certificates or add it to trusted docker certs.
To work with other tools besides Docker the best idea is to install our certificate.

> We should do next steps on each node in which we want to use our local registry. Choose one of bellow ways

####  I. Installing a certificate
```ShellSession
sudo cp /certs/domain.crt /usr/share/ca-certificates
sudo chmod 644 /usr/share/ca-certificates/domain.crt
sudo dpkg-reconfigure ca-certificates
```
Reconfigure window will appert. We should set checkbox on domain.crt by pushing space button and then accept by pushing enter.
Then restart docker
```ShellSession
sudo systemctl restart docker
```
  
####  II. Adding a certificate to trusted certs for docker
```ShellSession
sudo mkdir -p /etc/docker/certs.d/<NODE IP:5000>
sudo cp /certs/domain.crt /etc/docker/certs.d/<NODE IP>:5000/
cd /etc/docker/certs.d/<NODE IP>:5000/
sudo mv domains.crt ca.crt
```
After that reload docker daemon to use the certificate
```ShellSession
sudo service docker reload
```

### 3. Configure authentication
To connect to docker registry we need login and password. We need to create a password file with one entry for the user "user" with password "userpassword". 

We will create a fictive container and move the output of "htpasswd" tool to /auth/htpasswd. In this file we can find our login and hashed password

```ShellSession
sudo mkdir /auth
sudo touch /auth/htpasswd
sudo docker run --entrypoint htpasswd registry:2.7.0 -Bbn testuser testpassword > /auth/htpasswd
```

### 4. Creating container
Now we can create container with our registry
```ShellSession
sudo docker container stop registry
sudo docker run -d -p 5000:5000 --restart=always --name registry -v /auth:/auth -e "REGISTRY_AUTH=htpasswd" -e "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm" -e REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd -v /certs:/certs -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key registry:2
```
It must appear in the list of running containers
```ShellSession
sudo docker ps
```
To check the correctness push an image to our registry
```ShellSession
sudo docker login <NODE IP>:5000
sudo docker pull busybox
sudo docker images
sudo docker tag <IMAGE ID> <NODE IP>:5000/busybox
sudo docker push <NODE IP>:5000/busybox
```
The correct output is
```ShellSession
<IMAGE ID>: Pushed
latest: digest: sha256:<...> size: 527
```
We can check images in our repository be sending GET request to our docker registry
```ShellSession
curl -X GET https://<NODE IP>:5000/v2/_catalog
{"repositories":["busybox"]}
```
