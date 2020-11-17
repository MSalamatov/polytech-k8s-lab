## Creating Docker Local Registry

Docker Local Registry is an ordinary container upped on our node with registry image.

###   1. Certificate generation

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

### 2. Installing certificate
After this we will have domain.crt and domain.key in /certs
We can install certificate by adding it to ca-certificates or add it to reliable docker certs.
To work with other tools besides Docker the best idea is to install it.

> We should do next steps on each node we want to use our local registry. Choose one of bellow ways

####  I. Installing certificate
```ShellSession
sudo cp /certs/domain.crt /usr/share/ca-certificates
sudo chmod 644 /usr/share/ca-certificates/domain.crt
sudo dpkg-reconfigure ca-certificates
```
Reconfigure window will appert. We should set checkbox on domain.crt by pushing space button and then accept by pushing enter.
Then restart docker
  
####  II. Adding certificate to reliable certs for docker

