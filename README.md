
1. Login

https://eu-west-2.console.aws.amazon.com/console/home?region=eu-west-2#

2. Go to Lightsail



3. Loginto Bitnami


4. 

5. Setup variables for domain and email

export DOMAIN=www.seofixdaily.com
export EMAIL=andrew@mcgarryfashion.com

5. Install Lego



cd /tmp
curl -s https://api.github.com/repos/xenolf/lego/releases/latest | grep browser_download_url | grep linux_amd64 | cut -d '"' -f 4 | wget -i -
tar xf lego_linux_amd64.tar.xz
sudo mv lego_linux_amd64 /usr/local/bin/lego



sudo /opt/bitnami/ctlscript.sh stop

sudo lego --email="EMAIL-ADDRESS" --domains="DOMAIN" --path="/etc/lego" run


sudo mv /opt/bitnami/apache2/conf/server.crt /opt/bitnami/apache2/conf/server.crt.old
sudo mv /opt/bitnami/apache2/conf/server.key /opt/bitnami/apache2/conf/server.key.old
sudo mv /opt/bitnami/apache2/conf/server.csr /opt/bitnami/apache2/conf/server.csr.old
sudo ln -s /etc/lego/certificates/DOMAIN.key /opt/bitnami/apache2/conf/server.key
sudo ln -s /etc/lego/certificates/DOMAIN.crt /opt/bitnami/apache2/conf/server.crt
sudo chown root:root /opt/bitnami/apache2/conf/server*
sudo chmod 600 /opt/bitnami/apache2/conf/server*
