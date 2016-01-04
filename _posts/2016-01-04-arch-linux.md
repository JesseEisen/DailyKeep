---
title: Config Arch Linux
updated: 2016-01-04 21:09
---

## Configure wired connect
when i installed arch linux, i use the wifi, just use the command `wifi-menu xxx`(the xxx can use `iw dev`, the name of the wireless network).
but when we use the ADSL to connect to the network, we just need to install one tool `rp-pppoe`. This tool needs to install first.

Then we just need to use this `pppoe-setup`, according the guide, set your account name, your passwd, the DNS and the firewall type. After
setup finished, we just use `pppoe-start` and wait a minute. if it show `connected` that's mean connected successfully! Try `ping xxx`. 

If we want it to connect automaticaly, we just use this command `systemctl enable adsl`.

## Install ssh

When we want to generate the ssh-key, we need use `ssk-keygen` but we need to install `ssh` first. Try `pacman -Sy openssh`
and the follow the guide of the github's `Generating SSH keys`


