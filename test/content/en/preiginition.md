---
title: "Hack The Box - Preiginition"
date: 2022-10-16T13:05:03+05:30
draft: false
description: " Preigination from HackTheBox is an retired machine which is vulnerable to security Misconfiguration, which can be easily exploited with help of default credential "
hideToc: false
tocPosition: outer
enableToc: true
enableTocContent: false
author: Atom
authorEmoji: ✍️
tags:
- HTTP
- Defult Credentials 
series:
- Starting Point Tire 0
categories:
- Very Easy
- HackTheBox
- Linux
libraries:

image: /images/hackthebox/preiginition/1.png 
---

Hi Hackers Welcome Back, Today we are going to look at Hack The Box Preiginition Machine.

## Info Table
<table style="width:100%">
 <thead>
  <tr>
    <th>Room Name</th>
    <th>Preiginition</th>
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
While enumerating ports and services we can able to find the port 80 ware open. fuzzed directory and found hidden page also bypassed using default credentials.

## Recon

let’s start with basic recon. 

```jsx
root@atom-VirtualBox:/home/atom/htb/preignition# nmap -sV 10.129.145.244
Starting Nmap 7.80 ( https://nmap.org ) at 2022-10-11 13:18 IST
Nmap scan report for 10.129.145.244
Host is up (0.16s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE VERSION
80/tcp open  http    nginx 1.14.2

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 9.93 seconds
root@atom-VirtualBox:/home/atom/htb/preignition#
```

Port 80 has been opened. while visiting target port we can able to see that default web page of nginx ware rendered.

{{< img src="/images/hackthebox/preiginition/preiginition2.png" alt="image alt" width="1000px" position="center" >}}

When browsing a regular web page, we use these elements to move around on the website. However, these elements are only links to other directories containing other web pages, which get loaded in our browser as if we manually navigated to them using the URL search bar at the top of the browser screen. Knowing this, could we attempt to find any "hidden" content hosted on this webserver?

For finding hidden directory or page we will use gobuster which is pre installed in major pentest os if not  ``` sudo apt-get install gobuster ```  command is used to install targeted for Debian destributions.

we will common.txt which is located under ``` usr/share/wordlist/dirb/common.txt ``` if not download it from github

```jsx
root@atom-VirtualBox:/home/atom/htb/preignition# gobuster -e -u http://10.129.146.23:80 -w /usr/share/wordlist/dirb/common.txt

=====================================================
Gobuster v2.0.1              OJ Reeves (@TheColonial)
=====================================================
[+] Mode         : dir
[+] Url/Domain   : http://10.129.146.23:80/
[+] Threads      : 10
[+] Wordlist     : /usr/share/wordlist/dirb/common.txt
[+] Status codes : 200,204,301,302,307,403
[+] Expanded     : true
[+] Timeout      : 10s
=====================================================
2022/10/11 13:09:25 Starting gobuster
=====================================================
http://10.129.146.23:80/admin.php (Status: 200)
Progress: 1314 / 4615 (28.47%)^C
```

we can see that admin.php ware hidden and it has  admin login page. using default credential ``` admin:admin ``` we have bypassed and accessed flag

{{< img src="/images/hackthebox/preiginition/preiginition3.png" alt="image alt" width="1000px" position="center" >}}
