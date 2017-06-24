---
published: true
layout: post
title: Create Music Streaming Server with Orange Pi Zero
comments: true
description: How to create music streaming server with orange pi
keywords: 'mpd, mopidy, orange pi zero, streaming server'
categories: howto
---

I have a Debian Jessie based on [local torrent server](https://www.perdanahadisanjaya.cf/2017/setup-deluge-webui-in-headless-server/) and I want to make it able play *ehem* audio files. While on quest on an open source streaming server I found **Mopidy**.

Mopidy is an extensible music server that plays music from local disk, Spotify, SoundCloud, Google Play Music, and more. You edit the playlist from any phone, tablet, or computer using a range of MPD and web clients.

Here is the steps:

### Install Mopidy

**Add the archive’s GPG key**

```
$ wget -q -O - https://apt.mopidy.com/mopidy.gpg | sudo apt-key add -
```

**Add the APT repo to your package sources**

```
 $ sudo wget -q -O /etc/apt/sources.list.d/mopidy.list https://apt.mopidy.com/jessie.list
```

**Install Mopidy and all dependencies**

```
$ sudo apt-get update
$ sudo apt-get install mopidy
```

**Set a couple of config values**

See your default `~/.config/mopidy/mopidy.conf` file and edit it into your needs. For me I need to add Spotify so I do this.

```
$ vim ~/.config/mopidy/mopidy.conf
```

```
[mpd]
hostname = ::

[spotify]
username = perdanahadisanjaya
password = krustycrabsecretrecipes
```

Check your config

```
$ mopidy config
```

**Start Mopidy**

You can start `mopidy` by issuing command

```
$ mopidy
```

It should return something like this

```
MPD server running at [127.0.0.1]:6600
```

You can search any **MPD** clients [here](https://docs.mopidy.com/en/latest/clients/mpd/) and connect to **your-server-ip** with port **6600**.

By some time later if you decide to kill it

```
$ pkill mopidy
```

Simple isn't?

**Start Mopidy as services**

Enable mopidy at startup

```
$ sudo systemctl enable mopidy
```

The rest is standart services management

```
$ sudo systemctl start mopidy
$ sudo systemctl stop mopidy
$ sudo systemctl restart mopidy
```

You can check if Mopidy is currently running as a service by running:

```
$ sudo service mopidy status
```

---

### Mopidy Extension

[Here](https://docs.mopidy.com/en/latest/#ext) is full list of Mopidy extensions, how to install it refer to **Spotify Extension**

**Spotify Extension**

```
sudo apt-get install mopidy-spotify
```
---

### Source

[https://github.com/mopidy/mopidy](https://github.com/mopidy/mopidy)
