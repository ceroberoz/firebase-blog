---
published: true
layout: post
title: Fixing RTL8723BE in Fedora 25 and Ubuntu 16.04.2 LTS
comments: true
description: How to fixing RTL8723BE in Fedora 25 and Ubuntu 16.04.2 LTS
keywords: 'Realtek, RTL8723BE, Fedora 25, Ubuntu 16.04.2 LTS'
categories: troubleshoot
---

Do you get something like this symptom with your Wi-Fi?
- Cannot connect to Wireless Access Point
- Low signal coverage
- Using Realtek Wireless Card (for me it's RTL8723BE on both machine, duh.)

Well.. I have already wrote the troubleshoot for Ubuntu in [here](https://www.kaskus.co.id/thread/56d94d4250741031518b456a/guide-install-driver-wlan-realtek-rtl-series) and [here](https://devnotes-ceroberoz.rhcloud.com/2017/01/11/rtl8723be-ubuntu-fix/) but it's not gonna hurt to write another one.

This time I've got another notebook from office, it's HP Probook 440 G4 and somehow I got bored with Windows 10 (yay) and Ubuntu 16.04.2 LTS (too stable not much *fun*, as much as I expected from LTS version) and then I decide to install Fedora 25 for science and ALAS the same case happens with Fedora 25.

FYI, this **troubleshoot** does not apply only to RTL8723BE but also:
- RTL8192CE
- RTL8192SE
- RTL8192DE
- RTL8188EE
- RTL8192EE
- RTL8723AE
- RTL8723CE
- RTL8821AE

### Update AND Upgrade your system

You know the drill, right?

For Ubuntu 16.04.2 LTS

```
$ sudo apt-get update && sudo apt-get dist-upgrade -y
```

For Fedora 25

```
$ sudo dnf update && sudo dnf upgrade -y
```

Wait until finish, reboot if necessary and then we will proceed to the next step.

### Check your Realtek Version

We will use `lspci` to check your wireless version.
```
$ lspci
```

It should return something like this.

```
...
02:00.0 Network controller: Realtek Semiconductor Co., Ltd. RTL8723BE PCIe Wireless Network Adapter
...
```

Now we've got the Realtek version, we will save this for later.

### Get the latest Realtek Driver

We will clone repository `rtlwifi_new` from `lwfinger`
```
$ git clone https://github.com/lwfinger/rtlwifi_new.git
```
Change to directory `rtlwifi_new`
```
$ cd rtlwifi_new/
```
Checkout to `rock.new_btcoex` branch
```
$ git checkout rock.new_btcoex
```
From what I know, this is something like *nightly* driver.

### Install the requirements for compile Realtek Driver

We will install the requirements to compile Realtek Driver.

For Fedora 25 it's `gcc` and `kernel-devel` 
```
$ sudo dnf install kernel-devel gcc
```
For Ubuntu 16.04.2 LTS
```
$ sudo apt-get install linux-headers-$(uname -r) gcc g++ build-essential dkms git
```
### Compile and install

Still in directory `rtlwifi_new`, run `make` and `sudo make install` to install the driver.

```
$ make clean
$ make
$ sudo make install
```

Remove old drivers
```
$ sudo modprobe -rv rtl8723be
rmmod rtl8723be
rmmod rtl_pci
rmmod btcoexist
rmmod rtlwifi
rmmod mac80211
rmmod cfg80211
```

### Set `ant_sel` value

Now we will repopulate by set the right `ant_sel` value, it's vary from 0 to 5.

Try to start from 0 and check the wireless radio with **Network Manager**, if you can pickup signal then you've got the right `ant_sel` value. If not, return to step **Compile and install**.

As for me, I've got it with value 2.

```
$ sudo modprobe -v rtl8723be ant_sel=2
insmod /lib/modules/4.10.10-200.fc25.x86_64/kernel/net/wireless/cfg80211.ko.xz
insmod /lib/modules/4.10.10-200.fc25.x86_64/kernel/net/mac80211/mac80211.ko.xz
insmod /lib/modules/4.10.10-200.fc25.x86_64/kernel/drivers/net/wireless/realtek/rtlwifi/rtlwifi.ko.xz
insmod /lib/modules/4.10.10-200.fc25.x86_64/kernel/drivers/net/wireless/realtek/rtlwifi/rtl_pci.ko.xz
insmod /lib/modules/4.10.10-200.fc25.x86_64/kernel/drivers/net/wireless/realtek/rtlwifi/btcoexist/btcoexist.ko.xz
insmod /lib/modules/4.10.10-200.fc25.x86_64/kernel/drivers/net/wireless/realtek/rtlwifi/rtl8723be/rtl8723be.ko.xz ant_sel=2
```

Save the changes
```
$ echo "options rtl8723be ant_sel=2" | sudo tee /etc/modprobe.d/rtl8723be.conf
options rtl8723be ant_sel=2
```

---

### Summary

By now, your Realtek driver should be installed and working flawlessly. But please take note, this is **a temporary solution** until there is an official fix or some other work around, you tell me.

If there is a kernel update, you must repeat this from step **Compile and install**. Not much but it's handy to remember these step or bookmark this page.

---

### Source

[https://www.kaskus.co.id/thread/56d94d4250741031518b456a/guide-install-driver-wlan-realtek-rtl-series](https://www.kaskus.co.id/thread/56d94d4250741031518b456a/guide-install-driver-wlan-realtek-rtl-series)

[https://devnotes-ceroberoz.rhcloud.com/2017/01/11/rtl8723be-ubuntu-fix/](https://devnotes-ceroberoz.rhcloud.com/2017/01/11/rtl8723be-ubuntu-fix/)

[https://chirath02.wordpress.com/2016/05/12/installing-realtek-rtl8723be-driver-for-ubuntu-16-04/](https://chirath02.wordpress.com/2016/05/12/installing-realtek-rtl8723be-driver-for-ubuntu-16-04/)

[https://askubuntu.com/questions/635625/how-do-i-get-a-realtek-8723be-wireless-card-to-work](https://askubuntu.com/questions/635625/how-do-i-get-a-realtek-8723be-wireless-card-to-work)
