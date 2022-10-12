---
title: "Hack The Box - Explosion"
date: 2022-10-12T10:23:58+05:30
draft: false
description: " Meow from HackTheBox is an retired machine which is vulnerable to Telnet and security Misconfiguration, which can be easily exploited with help of Basic Recon "
hideToc: false
tocPosition: outer
enableToc: true
enableTocContent: false
author: Atom
authorEmoji: ✍️
tags:
- RDP
series:
- Starting Point Tire 0
categories:
- Very Easy
- HackTheBox
- Linux
libraries:

image: /images/hackthebox/explosion/explosion1.png 
---

Hi Hackers Welcome Back, Today we are going to look at Hack The Box Explosion Machine.

## Info Table
<table style="width:100%">
 <thead>
  <tr>
    <th>Room Name</th>
    <th>Explosion</th>
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
While enumerating ports and services we can able to find the port 3389 ware open. while trying to connect with default or well known usernames. RDP ware get logged in with default security credentials.

## Recon

Let’s start with nmap Default scan ( -sC ) resulting port 3389 RDP port has opened and the target is windows  2    platform operating system 

```bash
atom@atom-VirtualBox:~/htb/explosion$ nmap -sC -sV 10.129.1.13 -o initial.txt
Starting Nmap 7.80 ( https://nmap.org ) at 2022-10-12 09:27 IST
Nmap scan report for 10.129.1.13
Host is up (0.16s latency).
Not shown: 996 closed ports
PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: EXPLOSION
|   NetBIOS_Domain_Name: EXPLOSION
|   NetBIOS_Computer_Name: EXPLOSION
|   DNS_Domain_Name: Explosion
|   DNS_Computer_Name: Explosion
|   Product_Version: 10.0.17763
|_  System_Time: 2022-10-12T03:58:17+00:00
| ssl-cert: Subject: commonName=Explosion
| Not valid before: 2022-10-11T03:55:24
|_Not valid after:  2023-04-12T03:55:24
|_ssl-date: 2022-10-12T03:58:26+00:00; 0s from scanner time.
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2022-10-12T03:58:20
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 54.45 seconds
```

Remote access software represents a legitimate way to connect to other hosts to perform actions or offer support. The interactions involved by using any type of remote access tool can either be CLI-based Command Line Interface) or GUI-based (Graphical User Interface). These tools use the same protocol at their base to communicate with the other hosts, which is RDP . RDP (Remote Desktop Protocol) operates on ports 3389 TCP and 3389 UDP . The only difference consists of how the information relayed by this protocol is presented to the end-user.

**CLI - Remote Access Tools**

Command Line Interface-based Remote Access Tools have been around forever. A rudimentary example of this is ``` Telnet ```, which was explored briefly in the ``` Meow ``` machine. In its most basic configuration, Telnet is considered insecure due to lacking the ability to encrypt the data being sent through it securely. This implies that an attacker with access to a network TAP (Traffic Access Point) could easily intercept the packets being sent through a Telnet connection and read the contents, be they login credentials, sensitive files, or anything else. Telnet, which runs on port 23 TCP by default, has mainly been replaced by its more secure counterpart, SSH , running on port 22 TCP by default.

``` SSH ``` , which stands for Secure Shell Protocol, adds the required layers of authentication and encryption to the communication model, making it a much more viable approach to perform remote access and remote

file transfers. It is used both for patch delivery, file transfers, log transfer, and remote management in today's environment.

However, both Telnet and SSH only offer the end-user access to the remote terminal part of the host being reached. This means that no display projection comes with these tools. In order to be able to see the remote host's display, one can resort to CLI-based tools such as ``` xfreerdp ``` . Tools such as this one are called ``` Remote Desktop Tools``` , despite being part of the ``` Remote Access family.``` The reasoning behind this is because the whole desktop can be remotely controlled by the user initiating the connection, like one would if they were physically in the room with the remote host, using its keyboard, mouse, and display to interact with it, including the ability to view graphical content, controlling the mouse pointer and keyboard input,  easily interacting with the web browser, and more.

We can quickly check for any misconfigurations in access control by attempting to connect to this readily available port without any valid credentials, thus confirming whether the service allows guest or anonymous connections or not.

we will be using xfreerdp to connect from our Parrot Security virtual machine. to target machine. If you need to install xfreerdp, you can proceed with the following command: ``` sudo apt-get install freerdp2-x11 ``` 

```jsx
atom@atom-VirtualBox:~/htb/explotion$ sudo apt install freerdp2-x11
[sudo] password for atom: 
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following NEW packages will be installed:
  freerdp2-x11
0 upgraded, 1 newly installed, 0 to remove and 4 not upgraded.
Need to get 96.8 kB of archives.
After this operation, 830 kB of additional disk space will be used.
Get:1 http://in.archive.ubuntu.com/ubuntu jammy-updates/universe amd64 freerdp2-x11 amd64 2.6.1+dfsg1-3ubuntu2.2 [96.8 kB]
Fetched 96.8 kB in 1s (70.0 kB/s)       
Selecting previously unselected package freerdp2-x11.
(Reading database ... 214147 files and directories currently installed.)
Preparing to unpack .../freerdp2-x11_2.6.1+dfsg1-3ubuntu2.2_amd64.deb ...
Unpacking freerdp2-x11 (2.6.1+dfsg1-3ubuntu2.2) ...
Setting up freerdp2-x11 (2.6.1+dfsg1-3ubuntu2.2) ...
Processing triggers for man-db (2.10.2-1) ...
```

``` /v:{target_IP} : ``` Specifies the target IP of the host we would like to connect to.

``` /cert:ignore :``` Specifies to the scrips that all security certificate usage should be ignored.

``` /u:Administrator :``` Specifies the login username to be "Administrator".

``` /v:{target_IP} :``` Specifies the target IP of the host we would like to connect to.

Login with common user names and default password after many tries. we got RDP desktop using user name as  Administrator with empty password.

```bash
atom@atom-VirtualBox:~/htb/explosion$ xfreerdp /v:10.129.1.13 /cert:ignore /u:Administrator
[10:20:11:944] [10432:10433] [INFO][com.freerdp.core] - freerdp_connect:freerdp_set_last_error_ex resetting error state
[10:20:11:944] [10432:10433] [INFO][com.freerdp.client.common.cmdline] - loading channelEx rdpdr
[10:20:11:946] [10432:10433] [INFO][com.freerdp.client.common.cmdline] - loading channelEx rdpsnd
[10:20:11:946] [10432:10433] [INFO][com.freerdp.client.common.cmdline] - loading channelEx cliprdr
[10:20:11:955] [10432:10433] [INFO][com.freerdp.client.x11] - Property 357 does not exist
[10:20:11:280] [10432:10433] [INFO][com.freerdp.primitives] - primitives autodetect, using optimized
[10:20:11:288] [10432:10433] [INFO][com.freerdp.core] - freerdp_tcp_is_hostname_resolvable:freerdp_set_last_error_ex resetting error state
[10:20:11:288] [10432:10433] [INFO][com.freerdp.core] - freerdp_tcp_connect:freerdp_set_last_error_ex resetting error state
[10:20:12:658] [10432:10433] [INFO][com.freerdp.crypto] - creating directory /home/atom/.config/freerdp
[10:20:12:658] [10432:10433] [INFO][com.freerdp.crypto] - creating directory [/home/atom/.config/freerdp/certs]
[10:20:12:658] [10432:10433] [INFO][com.freerdp.crypto] - created directory [/home/atom/.config/freerdp/server]
Password:
```
{{< img src="/images/hackthebox/explosion/explosion2.png" alt="image alt" width="600px" position="center" >}}