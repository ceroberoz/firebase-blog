---
published: true
layout: post
title: Create VirtualHost Apache2 Ubuntu 16.04
comments: true
description: How to Create VirtualHost with Apache2 on Ubuntu 16.04
keywords: 'VirtualHost, Apache2, Ubuntu 16.04'
categories: linux-server
---

Pretext: I have a low usage VPS for low traffic web based project, it cost me about USD $2.5/mo and it's only use 1/4 from it's total resources usage. My friend need a  web host and cannot afford for extra fees since he rarely use it.

So here I am create a VirtualHost on VPS for science! :D

### Configure your server
```
$ cd /etc/apache2/sites-available
```

Copy you default `000-default` in case you forgot the default config.
```
$ sudo cp 000-default.conf 000-sandbox.conf
```

Edit `000-sandbox.conf` to your desireable value, the important point is:

- Set your IP address & port to `VirtualHost` listening to, I only need `127.0.0.1` as IP address &  as port `80`

- Be sure to set the `DocumentRoot` directory into correct path, for me it's `/var/www/html/sandbox`

- I will use `sandbox.kerekeren.id` as valid sub-domain for `ServerAlias` & `ServerName`.

- I want separate `ErrorLog` & `CustomLog` log for this VirtualHost.


The final config for `000-sandbox.conf` will be something like this.

```
<VirtualHost 127.0.0.1:80>
        ServerAdmin kerekeren.id@gmail.com
        DocumentRoot /var/www/html/sandbox
        ServerAlias wwww.sandbox.kerekeren.id
        ServerName sandbox.kerekeren.id

        ErrorLog ${APACHE_LOG_DIR}/error_sandbox.log
        CustomLog ${APACHE_LOG_DIR}/access_sandbox.log combined

</VirtualHost>
```

Activate your virtualhost
```
$ sudo a2ensite 000-sandbox.conf
```

Restart Apache to apply the changes
```
$ sudo service apache2 restart
```

### Add New Domain or Sub-Domain
```
$ sudo vim /etc/hosts
```

Add your domain or sub-domain under `localhost`
```
$ 127.0.0.1       localhost kerekeren.id sandbox.kerekeren.id
```

Save the changes and proceed to the next step.

### Configure your DNS Manager

I am using CloudFlare (CF) as DNS Manager, so if you're not using CF then you have to improvise :D

Open your DNS Manager, add **A** record with your desireable **domain** AND **your-server-ip**

![CloudFlare DNS Manager]({{site.baseurl}}/static/img/_post/create-virtualhost-apache2-ubuntu-16.04/manage-cf-dns.png)

Done

---

### Extras

**Rollback: Disable VirtualHost**

by some time later if you want to disable the VirtualHost, just do
```
$ sudo a2dissite 000-sandbox.conf
```

to disable the VirtualHost.

**Security: Secure DocumentRoot directory permission**

Secure DocumentRoot directory permission
```
$ sudo chmod 755 /var/www/html
```

Set owner & permission into `sandbox` directory
```
$ sudo chown sandbox:www-data -R /var/www/html/sandbox
```

```
$ sudo chmod 775 -R /var/www/html/sandbox
```

**Security: Create a dedicated non-root user for VirtualHost**

Add new user
```
$ sudo useradd sandbox
```

Set new password for user `sandbox`
```
$ sudo passwd sandbox
```

Assign user `sandbox` into group `www-data`
```
$ sudo usermod -aG sandbox www-data
```

**Security: Set default login path into VirtualHost web root directory**
```
$ sudo vim /etc/passwd
```

Change defaut sandbox to something like this
```
$ sandbox:x:1002:1002::/var/www/html/sandbox:
```
---

### Source

This guide is based from my personal experience and yes it's tested in real life, you may modify this into your need. Feel free to ask me on Disqus if you need some assistance. :)
