---
published: true
layout: post
title: Automate deploy Git with cron
comments: true
description: How to automate boring work into less boring work
keywords: 'git, github, bitbucket, cron, automate'
categories: howto
---

In workspace, I do work as both coder and versioning manager. As fellow coder I am sure you'll get annoyed when you *being serious* in coding and some ~~ignorant person~~ workmates wants to deploy their commit into server.

FYI, the current flow is something like this:
```
developer commit & push to branch -> me login into server -> me pull the changes
```

The question is, why don't we automate that boring work?

After googling, at least I found two method to set git auto deploy method:

- the one with git bare and hooks in your-own-private git server
- the one with cron and ssh

And yes both method do work fine.

In this post I will go with **cron and ssh** method because I can still use github and bitbucket so I could use their services while I can check the changes online with web or android apps and get the code deployed into server.

As for the method, here we go.

### Login as web user from ssh

From your linux or any ssh client, do login into your server. `root` user can be used to but it's better to use you user that in `www-data` group.

```
$ ssh youruser@your-server-ip
```

Congrats, now you should be on server!

### Generate ssh key

We want git to pull from server, so we do set this up on server.

First, generate ssh key
```
$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/perdanahadi/.ssh/id_rsa):
```

Please note we do not need password to protect ssh so skip the password with enter.

If succeed, it will return something like this

```
$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/perdanahadi/.ssh/id_rsa):
Created directory '/Users/emmap1/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/perdanahadi/.ssh/id_rsa.
Your public key has been saved in /home/perdanahadi/.ssh/id_rsa.pub.
The key fingerprint is:
4c:80:61:2c:00:3f:9d:dc:08:41:2e:c0:cf:b9:17:69 perdanahadi@serverkere
The key's randomart image is:
+--[ RSA 2048]----+
|*o+ooo.          |
|.+.=o+ .         |
|. *.* o .        |
| . = E o         |
|    o . S        |
|   . .           |
|     .           |
|                 |
|                 |
+-----------------+
```

Later, check the keys

```
$ ls -a ~/.ssh
```

### Start ssh-agent and load your keys

First thing first, check if the agent is running
```
$ ps -e | grep [s]sh-agent
9060 ?? 0:00.28 /usr/bin/ssh-agent -l
```

If not, start the services manually
```
$ ssh-agent /bin/bash
```

Load your ssh-keys into `ssh-agent`
```
$ ssh-add ~/.ssh/id_rsa
Enter passphrase for /home/perdanahadi/.ssh/id_rsa:
Identity added: /home/perdanahadi/.ssh/id_rsa (/home/perdanahadi/.ssh/id_rsa)
```

Check if keys already installed
```
$ ssh-add -l
2048 7a:9c:b2:9c:8e:4e:f4:af:de:70:77:b9:52:fd:44:97 /home/perdanahadi/.ssh/id_rsa (RSA)
```

### Install your public key into GitHub or BitBucket

From your server, copy paste the value from you public key.

If you follow my guide, it's on `/home/perdanahadi/.ssh/id_rsa.pub`

```
$ cat /home/perdanahadi/.ssh/id_rsa.pub
```

**GitHub**

Add your new SSH key from here

```
https://github.com/settings/keys
```

**BitBucket**

Add your new SSH key from here

```
https://bitbucket.org/account/user/yourusername/ssh-keys/
```

### Create a cron

```
$ crontab -e
```

After a `cron` config (?) show, put this cron

```
*/1 * * * * cd /path/to/webroot && git fetch --all && git checkout --force "origin/master"
```

**Explanation**:
- `*/1 * * * *` = Check the changes every 1 minute. You can set it to you preference time.
- `cd /path/to/webroot` = We change to web directory that has a `git` inside it.
- `&&` = AND
- `git fetch --all` = Get all changes from server but don't merge them
- `&&` = AND
- `git checkout --force "origin/master"` = Change to origin, master branch. this will remove your previously edit on server.

That's all!

---

### Extras

**What if I have both multiple branch and domain and want it to automate that boring stuff?**

It's simple, just set your branch name and add into last line of your `crontab -e`.

Example for `kerekeren.id` and branch `master`
```
*/1 * * * * cd /path/to/webroot && git fetch --all && git checkout --force "origin/master"
```

Example for domain `dev.kerekeren.id` and branch `dev`
```
*/1 * * * * cd /path/to/webroot && git fetch --all && git checkout --force "origin/dev"
```

**How to migrate to from `dev` branch into `master`**

Just `merge` your branch from `dev` into `master` from GitHub or BitBucket from your `localhost` and wait the ~~magic~~ `cron`.
```
$ git fetch origin master
$ git merge master
$ git push origin dev:master
```

---

### Source

[https://www.portent.com/blog/design-dev/github-auto-deploy-reprise.htm](https://www.portent.com/blog/design-dev/github-auto-deploy-reprise.htm)
[https://confluence.atlassian.com/bitbucket/set-up-ssh-for-git-728138079.html](https://confluence.atlassian.com/bitbucket/set-up-ssh-for-git-728138079.html)
[https://stackoverflow.com/questions/14168677/merge-development-branch-with-master](https://stackoverflow.com/questions/14168677/merge-development-branch-with-master)
