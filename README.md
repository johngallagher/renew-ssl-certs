

# SSL certificates and Lightsail

## What's an SSL certificate?

* Websites use HTTPS to encrypt information between the server and the browser
* This makes the connection secure - Google will heavily penalise sites not using HTTPS
* HTTPS uses an SSL certificate to tell the browser your site is who you say you are - it's like a passport


## Why do we need to renew certificates?

* Certificates expire after a certain date
*  https://letsencrypt.org/ can be used to create certificates for free
* The drawback - these certificates expire a few months after they've been issued
* When a certificate expires, bad things happen...
* The user is shown a red scary screen saying "This connection is insecure" and won't show your website unless the user says "OK, I don't care"
* Google classes your website as insecure
* Loads of other stuff breaks

## How do I find out when a certificate expires?

* Click on the padlock by the site, then click on "Certificate":
 
<img width="501" alt="untitled 9" src="https://user-images.githubusercontent.com/22185/41511972-632b4b64-7279-11e8-9434-77e7661ca67d.png">

* You can see this certificate is valid. Goody.

<img width="482" alt="untitled 10" src="https://user-images.githubusercontent.com/22185/41511981-97519024-7279-11e8-819e-634e5ffe7899.png">

Twirl down "Details", scroll down, and there's the expiry date:

<img width="777" alt="untitled 11" src="https://user-images.githubusercontent.com/22185/41511994-e77ec90e-7279-11e8-8cd9-b2b3abb91aa8.png">


## What is Lightsail? Bitnami?

### Lightsail

* Hosting your own Wordpress is a nightmare
* Lightsail is a one click service AWS provide for hosting Wordpress easily

### Bitnami

* The software bit of Lightsail
* It's a collection of different libraries and bits of software that give you a working Wordpress install
* AWS install it for you
* https://en.wikipedia.org/wiki/Bitnami

# How to setup certificates in Bitnami


## 1. Login to AWS

Click here:

https://eu-west-2.console.aws.amazon.com/console/home?region=eu-west-2#

* Enter the username and password (get these from Andrew)
* Press Login

## 2. Go to Lightsail

<img width="863" alt="untitled 2" src="https://user-images.githubusercontent.com/22185/41510634-167d6f90-7260-11e8-8603-4ab3120e7eef.png">


## 3. Login to Bitnami

Find the Bitnami install you want. In this example we'll use ppcfixdaily.

![pageshot of instances lightsail 2018-06-00-1701 36](https://user-images.githubusercontent.com/22185/41510612-b3ee02f4-725f-11e8-8faa-20cda16065be.png)

You'll see a window like this:

<img width="1012" alt="untitled 3" src="https://user-images.githubusercontent.com/22185/41512152-8cb63acc-727c-11e8-9fa7-78e8b78bde2c.png">

This is called **the terminal**. 

Typing commands in here and will affect your website, so **be careful!**

## Interlude: Practice how to copy and paste commands

You can type everything out from scratch but:

* You'll make mistakes
* You'll waste a ton of time
* You'll go crazy in about 5 minutes

The smart way is to copy and paste in the chunks of commands we need.

Let's practice that first.

### A. Click on the paste icon.

<img width="1012" alt="untitled 3" src="https://user-images.githubusercontent.com/22185/41511855-faaf9984-7276-11e8-8ae6-ad47d2c85c56.png">

### B. Paste into this box the commands you want to insert at the cursor

Copy the following and paste into the box:

```bash
echo 'This window is pretty annoying to paste into.'
echo 'But we have superhero powers so we do not care.'
```

Here's what you should see:

<img width="684" alt="untitled 6" src="https://user-images.githubusercontent.com/22185/41511842-d7e5deea-7276-11e8-824a-70ba9542cafe.png">


### C. Double check this is what you want

Some commands can do some serious damage to your Bitnami install if entered incorrectly.

### D. Click on the clipboard icon a second time

<img width="684" alt="untitled 6" src="https://user-images.githubusercontent.com/22185/41510621-d5f6d7f4-725f-11e8-81fb-d8c4ada98a71.png">

The box will disappear. It looks like nothing has happened. But it's remembered your text.

### E. Right click anywhere in the window

The commands you pasted in will be executed immediately.

Sometimes the last command will just sit there, so you might need to press return one final time.

<img width="952" alt="untitled 7" src="https://user-images.githubusercontent.com/22185/41511870-32928ece-7277-11e8-9767-228f794f3317.png">


## 4. Setup domain and email

Now we're ready to start copying and pasting commands.

Copy and paste this into the terminal using the steps you just followed above.

```bash
export DOMAIN=<your domain name here complete with www>
export EMAIL=<email address you logged into aws with>
```

**Example**

```bash
export DOMAIN=www.example.org
export EMAIL=john@example.org
```

**Remember** you might need to hit return after pasting it in to execute the last one.

If you did it right you should see this:

<img width="831" alt="untitled 12" src="https://user-images.githubusercontent.com/22185/41512262-8eb288c0-727d-11e8-9761-45f0f62ddaaf.png">

**But... nothing happened!**

Yup. That means **it worked**.

The terminal is written by nerds so it doesn't say nice stuff like "It worked". 

It just sits there like a catatonic sloth.

## 3. Install Lego

No, not the fun stuff. The weird command line tool that allows us to create certificates.

Copy and paste this into the terminal:

```bash
# Install lego
cd /tmp
curl -s https://api.github.com/repos/xenolf/lego/releases/latest | grep browser_download_url | grep linux_amd64 | cut -d '"' -f 4 | wget -i -
tar xf lego_linux_amd64.tar.xz
sudo mv lego_linux_amd64 /usr/local/bin/lego
```

There will be a bunch of crap printed to the screen. Wait for it to stop.

And now for the scary bit...

## 4. Stop the website running

What?! Yeah.

You're going to have to stop your entire website running.

Don't worry, this process will only take a minute or so and we can bring the site up again.

Just don't go for lunch at this point...

Copy and paste into the terminal:

```
# Stop bitnami
sudo /opt/bitnami/ctlscript.sh stop

# Create certificate
sudo lego --email="$EMAIL" --domains="$DOMAIN" --path="/etc/lego" run
```

This will spit out a bunch of crap onto the screen:

<img width="994" alt="untitled 13" src="https://user-images.githubusercontent.com/22185/41512301-4f15d2a2-727e-11e8-86dd-4a05365fce4d.png">

It's asking you "Do you accept the TOS? Y/n"

Type "Y" for this and any other questions it asks.

## 5. Install the new certificate

We've created the certificate.

Now we need to move the certificate into the right place.

Copy and paste this into the terminal:

```bash
sudo mv /opt/bitnami/apache2/conf/server.crt /opt/bitnami/apache2/conf/server.crt.old
sudo mv /opt/bitnami/apache2/conf/server.key /opt/bitnami/apache2/conf/server.key.old
sudo mv /opt/bitnami/apache2/conf/server.csr /opt/bitnami/apache2/conf/server.csr.old
sudo ln -s /etc/lego/certificates/$DOMAIN.key /opt/bitnami/apache2/conf/server.key
sudo ln -s /etc/lego/certificates/$DOMAIN.crt /opt/bitnami/apache2/conf/server.crt
sudo chown root:root /opt/bitnami/apache2/conf/server*
sudo chmod 600 /opt/bitnami/apache2/conf/server*
```

## 6. Start up the website again

Copy and paste this into the terminal:

```
# Startup bitnami
sudo /opt/bitnami/ctlscript.sh start
```

Go to your site and double check it's up and running.

If something goes wrong here, shout for help and phone John!

## 7. Test the certificate is good

See the bit at the start about clicking on the padlock by your site.

Success? If so, high five yourself! But sadly we're not done. 

This certificate will expire in a few months, meaning we need to log in and do this crazy dance all over again.

If only we were using a tool that could automatically do this for us... ...oh wait...

## 8. Setup the certificate to automatically renew

We want the computer to run a script to automatically renew the certificate.

Copy and paste the following into the terminal:

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

All that voodoo should have created a script that gets run every day that renews the certificate.

Double check it's been installed properly by doing:

```
sudo crontab -l
```

You should see this:

<img width="801" alt="untitled 9" src="https://user-images.githubusercontent.com/22185/41512440-38fd0b14-7280-11e8-8f3b-f898d88b8ab5.png">

Double check the last line matches exactly. If it is...

## 9. Celebrate!

You've just navigated the insanely complex world of Bitnami and SSL certificates to setup HTTPS.

And now your website will be seen by Google as all secure and shit.

Nice one! 
