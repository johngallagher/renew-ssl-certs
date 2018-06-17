
## Login

https://eu-west-2.console.aws.amazon.com/console/home?region=eu-west-2#

## Go to Lightsail



## Login to Bitnami



## How to copy and paste

Copying and pasting code into this window is a bit mental.

1. Click on the paste icon.

2. Paste into this box what you want to paste at the cursor.

3. Double check the text in the box! There is no going back...

4. Right click anywhere in the window.


## Setup domain and email

This will mean you don't need to keep entering these things again and again. Win!

```bash
export DOMAIN=<your domain name here complete with www>
export EMAIL=<email address you logged into aws with>
```

### For example

```bash
export DOMAIN=www.example.org
export EMAIL=john@example.org


## Create certificate

```bash
# Install lego
cd /tmp
curl -s https://api.github.com/repos/xenolf/lego/releases/latest | grep browser_download_url | grep linux_amd64 | cut -d '"' -f 4 | wget -i -
tar xf lego_linux_amd64.tar.xz
sudo mv lego_linux_amd64 /usr/local/bin/lego

# Stop bitnami
sudo /opt/bitnami/ctlscript.sh stop

# Create certificate
sudo lego --email="$EMAIL" --domains="$DOMAIN" --path="/etc/lego" run
```

## Agree to the terms and conditions

Answer all the prompts confirming.

Type either "Y" or "Yes".

## Install the new certificate

```bash
sudo mv /opt/bitnami/apache2/conf/server.crt /opt/bitnami/apache2/conf/server.crt.old
sudo mv /opt/bitnami/apache2/conf/server.key /opt/bitnami/apache2/conf/server.key.old
sudo mv /opt/bitnami/apache2/conf/server.csr /opt/bitnami/apache2/conf/server.csr.old
sudo ln -s /etc/lego/certificates/$DOMAIN.key /opt/bitnami/apache2/conf/server.key
sudo ln -s /etc/lego/certificates/$DOMAIN.crt /opt/bitnami/apache2/conf/server.crt
sudo chown root:root /opt/bitnami/apache2/conf/server*
sudo chmod 600 /opt/bitnami/apache2/conf/server*

# Startup bitnami
sudo /opt/bitnami/ctlscript.sh start
```

## Auto renew the certificate

```bash
touch ~/renew-certificate.sh

echo '#!/bin/bash' > ~/renew-certificate.sh
echo '' >> ~/renew-certificate.sh
echo "sudo /opt/bitnami/ctlscript.sh stop apache" >> ~/renew-certificate.sh
echo "sudo /usr/local/bin/lego --email=\"$EMAIL\" --domains=\"$DOMAIN\" --path=\"/etc/lego\" renew" >> ~/renew-certificate.sh
echo "sudo /opt/bitnami/ctlscript.sh start apache" >> ~/renew-certificate.sh

chmod +x ~/renew-certificate.sh

(sudo crontab -l ; echo "  0 0 1 * * /home/bitnami/renew-certificate.sh 2> /dev/null")| sudo crontab -
```
