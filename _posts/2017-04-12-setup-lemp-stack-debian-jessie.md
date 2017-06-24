---
published: true
layout: post
title: LEMP Stack on Debian Jessie
description: Linux Nginx MariaDb PHP
comments: true
keywords: 'LEMP Stack, Nginx, MariaDb, PHP'
categories: linux-server
---

Pretext: I have an Single Board Computer (SBC), Orange Pi Zero with DietPi OS and it's based on Debian Jessie. I want to install web development enviroment to fit my web development activity.

The SBC is attached into my Local Area Network (LAN) and accessible by remote SSH.

So here what I do.

### Install & Configure Linux

> if you re already on linux skip to install nginx. if not bye.

### Install & Configure Nginx

**Install Nginx**

It's a straightforward process :)

> sudo apt-get install nginx

**Configure Nginx**

Set worker process Nginx to fit your total core(s). You may subtitute vim with your-favorite-text-editor.

> sudo vim /etc/nginx/nginx.conf

Set `worker_processes` to 4

> worker_processes 4;

Save the changes and restart Nginx

> sudo service nginx restart

Configure `/etc/nginx/sites-available/default`, add `index.php` into `index` section.

You can also set `root` directory to fit your needs, in my case I set it into `*/media/usb/http`

```
server {
      listen 80 default_server;
      listen [::]:80 default_server ipv6only=on;
      root /media/usb/http;
      index index.php index.html index.htm;
      # Make site accessible from http://localhost/
      server_name _;
}
```

Still on `/etc/nginx/sites-available/default`, please edit the `location ~ \.php$` section too.
```
location ~ \.php$ {
         try_files $uri =404;   ---------> Add this line
         fastcgi_split_path_info ^(.+\.php)(/.+)$;
         #       # NOTE: You should have "cgi.fix_pathinfo = 0;" in php.ini
         #
         #       # With php5-cgi alone:
         #         fastcgi_pass 127.0.0.1:9000;
         #       # With php5-fpm:
         fastcgi_pass unix:/var/run/php5-fpm.sock;
         fastcgi_index index.php;
         include fastcgi.conf;
    }
```

*PS: I added an extra line `try_files $uri =404;` to prevent zero day exploits.*

When you're done, check your config with `nginx -t` it should return something like this.

```
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

Restart nginx to apply changes

> sudo service nginx restart

### Install & Configure PHP

**Install PHP**

> sudo  php5-fpm php5-cli php5-mcrypt

**Configure PHP**

Update your `php.ini`

> sudo nano /etc/php5/fpm/php.ini

Find `cgi.fix_pathinfo=1`, uncomment it and set it to

> cgi.fix_pathinfo=0

Restart `php5-fpm` service to apply the changes

> sudo service php5-fpm restart

Check if `php5-fpm` already running by create a file into your `root web directory`

> sudo vim /media/usb/http/kepo.php

And added `phpinfo();` inside `kepo.php`
```
<?php
      phpinfo();
?>
```
Save it.

Now open `http://localhost/kepo.php` to see your `PHP` information.

### Install & Configure MariaDb

**Install MariaDb**

> sudo apt-get install mariadb-server mariadb-client

Check if service MariaDb alread running

> sudo service mysql status

Check and login to MariaDB

> sudo mysql -v -u root -p

To manage and administre MariaDb you could use.. (pick any)

Desktop based (Linux): MySQL Workbench (the only and only one native Linux desktop based MySQL manager, if you know what I mean)

Desktop based (Windows): MySQL Workbench, Navicat (my personal favorite), SQLyog

Web based (any platform): phpMyAdmin, Adminer (my personal favorite), db5

### Conclusion

Congrats, you should have a LEMP stack running on your Debian Jessie ;)

You may start web developer things like install PHP Framework like [CodeIgniter](https://codeigniter.com/) (Yeah, It's old and rusty but I'm loving it), [Laravel](https://laravel.com/) (you can try [Lumen](https://lumen.laravel.com/) for blazing fast RESTful development), or any your-favorite-PHP engine.

Just make sure to put these PHP files into `root` web directory (ie: `/var/www/html/codeigniter`).

Cheers!

---

### Source

[http://valuebound.com/resources/blog/lemp-installation-nginx-mariadb-php-and-phpmyadmin-ubuntu-1404-in-4-steps](http://valuebound.com/resources/blog/lemp-installation-nginx-mariadb-php-and-phpmyadmin-ubuntu-1404-in-4-steps)

[https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-16-04](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-16-04)

[https://sinaudev.org/server/install-nginx-di-arch-linux/](https://sinaudev.org/server/install-nginx-di-arch-linux/)
