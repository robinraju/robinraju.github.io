---
layout: single
title: Installing Let's encrypt SSL on Bitnami LAMP on EC2
read_time: true
category: developer
tags: [Letsencrypt, SSL, Apache, EC2]
header:
    teaser: /assets/images/posts/developer/lets-encrypt.png
comments: true
toc: true
toc_label: "Contents at a glance"
toc_icon: "sitemap"
author_profile: false
share: true
related: true
permalink: /:categories/:year-:month-:day-:title/
excerpt: Install and configure Let's encrypt, the free and legitimate SSL for a web site hosted on Bitnami LAMP Stack on Amazon EC2
published: true
---
[Let's Encrypt](https://letsencrypt.org/){:target="_blank"} is a free Certificate Authority (CA) that issues SSL certificates. 
You can use these SSL certificates to secure traffic to and from your website. Here is how I did it for a site hosted on AWS EC2
powered by [Bitnami LAMP](https://bitnami.com/stack/lamp){:target="_blank"}

### Prerequisites

- Deployed a Bitnami application(LAMP) on EC2 and the application is available at a public IP address.
- The necessary credentials to log in to the EC2 instance.
- You own a domain name.
- Configured the domain name's DNS record to point to the public IP address of your EC2 instance.

I assume all these prerequisites were completed and we are good to start setting up SSL for our website.

### Install Certbot
> [Certbot](https://certbot.eff.org/about/){:target="_blank"} is an easy-to-use automatic client that fetches and deploys SSL/TLS certificates for your webserver. Certbot was developed by EFF and others as a client for Let’s Encrypt and was previously known as “the official Let’s Encrypt client” or “the Let’s Encrypt Python client.” Certbot will also work with any other CAs that support the ACME protocol.

SSH into your EC2 instance and issue the following commands.
```sh
$ sudo apt-get install software-properties-common python-software-properties
$ sudo add-apt-repository ppa:certbot/certbot

$ sudo apt-get update

$ sudo apt-get install python-certbot-apache
```

### Generate A Let's Encrypt Certificate For Your Domain
We can use the Certbot tool installed previously to generate SSL certificate for our domain.
At this point we need to specify the webroot directory and the domain name.

```sh
$ sudo certbot certonly --webroot -w /home/bitnami/htdocs -d example.com -d www.example.com
```

### Take a backup of existing self signed certificate (optional)
Before proceeding to install the new certificate, take a backup of the existing certificate and key file.
Bitnami will ship one self-signed certificate along every installations.

```sh
$ sudo mv /opt/bitnami/apache2/conf/server.crt /opt/bitnami/apache2/conf/server.crt.backup

$ sudo mv /opt/bitnami/apache2/conf/server.key /opt/bitnami/apache2/conf/server.key.backup
``` 

### Configure The Web Server To Use The Let's Encrypt Certificate

Link the new SSL certificate and certificate key file to the correct locations. 
Update the file permissions to make them readable by the root user only. 
Remember to replace the "example.com" placeholder with your actual domain name.

1. Link SSL certificate and key file 
```sh
$ sudo ln -s /etc/letsencrypt/live/example.com/fullchain.pem /opt/bitnami/apache2/conf/server.crt
$ sudo ln -s /etc/letsencrypt/live/example.com/privkey.pem  /opt/bitnami/apache2/conf/server.key
```
2. Fix file permissions
```sh
$ sudo chown -R bitnami:daemon /etc/letsencrypt
$ sudo find /etc/letsencrypt -type d -exec chmod 0775 {} \;
$ sudo find /etc/letsencrypt -type f -exec chmod 0664 {} \;
```
3. Make certificates to be readable by the root user only
```sh
$ sudo chown root:root /opt/bitnami/apache2/conf/server*
$ sudo chmod 600 /opt/bitnami/apache2/conf/server*
```

### Restart Apache web server
We have installed the SSL certificate and now lets restart our web server.
```sh
$ sudo /opt/bitnami/ctlscript.sh restart apache
```

### Force HTTPS redirection

We can now configure Apache to force redirect all incoming http requests to https.
Add the following lines to file: */opt/bitnami/apache2/conf/bitnami/bitnami.conf*

```xml
<VirtualHost _default_:80>
.
.
 RewriteEngine On
 RewriteCond %{HTTPS} !=on
 RewriteRule ^/(.*) https://%{SERVER_NAME}/$1 [R,L]
.
.
</VirtualHost>
```

### Auto-renew your SSL certificates

Let's encrypt certificates has a validity of 90 days only. We need to renew it manually before its expiry.
To get rid of this head ache, lets setup an auto-renew script using a cron task.

**Edit crontab file**
```sh
$ sudo crontab -e
```
At the bottom of your crontab file, you will enter a script which will tell your server to check for certificate renewals once per week, and to automatically renew the certificates if they are about to expire.

```text
47 05,17 * * * sudo certbot renew && sudo /opt/bitnami/ctlscript.sh restart
```

**Congratulations!** You've successfully installed and configured your Let's Encrypt SSL certificates.