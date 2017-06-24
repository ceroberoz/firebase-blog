---
layout: post
title: Setup Deluge WebUI in Headless Server
comments: true
description: Setup Deluge WebUI in Headless Server
keywords: 'deluge, debian, orange pi zero'
published: true
categories: linux-server
---
Pretext: I want to download torrent all night using *COUGH* office Internet connection. I already have Orange Pi Zero running DietPi with [LEMP Stack](https://ceroberoz.github.io/2017/setup-lemp-stack-debian-jessie/) and it won't be hurt if I added more services, for science of course :p

### Install Deluge

Deluge is a BitTorrent client written in Python. Deluge is cross-platform, using a front and back end architecture where libtorrent, a software library written in C++ which provides the application's networking logic, is connected to one of various front ends (including a text console, a Web interface, and a graphical desktop interface using GTK+) through the project's own Python bindings.

Because we only need the service and web ui, so we will install ``deluged`` and ``deluge-webui``

> sudo apt-get install deluged deluge-web

Start the services

> deluge-web &

*I added '&' to send deluge-web service into background process. You may use ``fg`` to return it.*

Now open ``http://127.0.0.1:8112``, it should show a promp password. Enter **deluge** as password. (You can change it later)

Start the daemon, connect & happy ~~~torrent~~~ science-ing :p
