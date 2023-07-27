---
title: "Hack The Box - Appointment"
date: 2022-10-17T23:55:02+05:30
draft: false
description: " Appointment from HackTheBox is an retired machine which is vulnerable to SQL Injection attack, which can be easily exploited with help of Basic Recon "
hideToc: false
tocPosition: outer
enableToc: true
enableTocContent: false
author: Atom
authorEmoji: ✍️
tags:
- SQL
series:
- Starting Point Tire 1
categories:
- Very Easy
- HackTheBox
- Linux
libraries:

image: /images/hackthebox/Appointment/1.png 
---

Hi Hackers Welcome Back, Today we are going to look at Hack The Box Appointment Machine

## Recon

lets start with Basic Recon 

``` bash  {hl_lines=[23]}
atom@atom-VirtualBox:~$ nmap -sC -sV 10.129.152.162
Starting Nmap 7.80 ( https://nmap.org ) at 2022-10-17 16:15 IST
Nmap scan report for 10.129.152.162
Host is up (0.19s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.38 ((Debian))
|_http-server-header: Apache/2.4.38 (Debian)
|_http-title: Login

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 36.10 seconds

```
we came to know that port 80 is open 

{{< img src="/images/hackthebox/Appointment/2.png" alt="image alt" width="1000px" position="center" >}}

After trying many attacks finally.  login ware bypasses using SQL injection method , download the  <a href=https://www.vulnhub.com/entry/kioptrix-level-1-1,22/ > Payload </a>  capture the login request, send it to intruder, apply the downloaded  payload. 

{{< img src="/images/hackthebox/Appointment/3.png" alt="image alt" width="1000px" position="center" >}}

{{< img src="/images/hackthebox/Appointment/4.png" alt="image alt" width="1000px" position="center" >}}
