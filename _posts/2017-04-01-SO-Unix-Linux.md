---
title: Hightlight of SO Unix and Linux
updated: 2017-04-01  17:00
---

##### 1. need a tty to run sudo

when use ` ssh 'sudo ...'`, sometimes will get an error `sorry, you must have a tty to run sudo`
This's probably because your `/etc/sudioers` file  has `Defaults requiretty`

The solution can use `-tt` option on ssh and open `IPQoS`, so a better solution is :

```bash
ssh -o IPQos=throughput -tt host 'stty raw -echo; sudo cmd | cat' < <(cat)
```

Note that since the output of the remote command will go to a terminal, that will still affect its buffering (which will be line-based for many applications) and bandwidth efficiency since `TCP_NODELAY` is on. Also with `-tt`, ssh sets the IPQoS to lowdelay as opposed to throughput.



##### 2. check if a variable is set in bash
Just use the parameter expansion to do this. i.e.

```bash
if [ -z ${var+x} ]; then echo "var is unset"; else echo "var is set to '$var'";fi
```

the ${parameter+word} means if the parameter is `set and not NUll` or `set and NULL` ,the return val is word. 
more infomation can refer [parameter expasion](http://pubs.opengroup.org/onlinepubs/9699919799/utilities/V3_chap02.html#tag_18_06_02)
