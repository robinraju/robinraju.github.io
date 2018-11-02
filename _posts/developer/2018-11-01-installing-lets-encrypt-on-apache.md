---
layout: single
title: Installing Free SSL Certificate Using Let's Encrypt and Certbot
read_time: true
category: developer
tags: [Letsencrypt, SSL, Apache, Nginx]
header:
    teaser: /assets/images/posts/developer/lets-encrypt/lets-encrypt-certbot.png
comments: true
toc: true
toc_label: "Contents at a glance"
toc_icon: "sitemap"
author_profile: false
share: true
related: true
permalink: /:categories/:year-:month-:day-:title/
excerpt: Install and configure Let's encrypt, the free SSL certificate for you website.
published: true
---

SSL is essential for protecting your website, even if it doesn't handle sensitive information like credit cards. It provides privacy, critical security and data integrity for both your websites and your users' personal information.
Web browsers give visual cues, such as a lock icon or a green bar, to make sure visitors know when their connection is secured. This means that they will trust your website more when they see these cues and will be more likely to buy from you.

{% include figure image_path="/assets/images/posts/developer/lets-encrypt/https_icon.png" alt="Https icon" caption="https icon shown on browser if your website is secure" %}

Getting SSL certificates often cost you money. If you are looking for a free provider to issue unlimited SSL certificates,
here is one solution - ***Let's Encrypt***

## Installing Let's Encrypt on Apache

I assume you have already installed apache web server and using ubuntu.

### Modify the virtualhosts from apache config file

Find the default configuration file for your installed Apache server and modify it.

```sh
 $ sudo vim /etc/apache2/sites-available/000-default.conf
```
Look for 
```xml
<VirtualHost *:80> 
.
.
</Virtualhost>
```

modify it to your desired website address
eg: 
  ```xml
  ServerName  example.com
  ServierAlias www.example.com
```

### Install Certbot and python libraries

```sh
$ sudo apt-get update
$ sudo apt-get install software-properties-common
$ sudo add-apt-repository ppa:certbot/certbot
$ sudo apt-get update
$ sudo apt-get install python-certbot-apache
```

### Install certificate using certbot command

```sh
$ sudo certbot --apache
```

it will show the list of available domains you have added to VirtualHost.
Hit enter without selecting any number to select all domains.

Press Y  or N for other questions.

During installation if you are prompted with message like:

*Please choose whether or not to redirect HTTP traffic to HTTPS, removing HTTP access*

Select option 2 to redirect all requests to HTTPS.

### Enable Auto renewal

Let's Encrypt certificates are issued with a validity of 90 days. We need to manually renew it before its expiry.
We can make use of ***cron*** to schedule this task and automatically renew our certificate. 

We can test automatic renewal for our certificates by running this command:

```sh
$ sudo certbot renew --dry-run
```

To create Cron job to automate renewal, open the cron file by issuing the following command:

```sh
$ crontab -e
```

Enter the following line at the end of the file opened.

```sh
47 05,17 * * * certbot renew --quiet --post-hook "sudo service apache2 reload"
```

Thats all, you are done with installing SSL certificate on your website.

### Upgrade all insecure requests to https

This is an additional precaution to make sure your web server is serving https only.
Install the header module for apache as follows
```sh
$ sudo a2enmod headers
``` 

Add the following to apache configuration

```xml
<ifModule mod_headers.c>
Header always set Content-Security-Policy "upgrade-insecure-requests;"
</IfModule>
```

Restart Apache server

```sh
$ sudo service apache2 restart
``` 