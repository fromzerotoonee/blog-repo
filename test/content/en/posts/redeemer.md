---
title: "Hack The Box - Redeemer"
date: 2022-10-12T02:41:09+05:30
draft: false
description: " Redeemer from HackTheBox is an retired machine which is vulnerable to Redis and security Misconfiguration, which can be easily exploited with help of Basic Recon "
hideToc: false
enableToc: true
enableTocContent: false
author: Atom
authorEmoji: ✍️
tags:
- Redis
series:
- Starting Point Tire 0
categories:
- Very Easy
- HackTheBox
- Linux
libraries:

image: /images/hackthebox/redeemer/redeemer1.png
---
Hi Hackers Welcome Back, Today we are going to look at Hack The Box Redeemer Machine.

## Info Table
<table style="width:100%">
 <thead>
  <tr>
    <th>Room Name</th>
    <th>Redeemer</th>
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
While enumerating ports and services we can able to find the port 6379 ware open. while tryed to connect without using any password. we got connected to Redis and get downloaded sensitive files. 

## Recon

let’s start with initial default scan (-sC). we did’t get any results, it shows that there is no services running on top 1000 ports. since we don’t know  vulnerable target port  so we can scan first 10000 ports 

```jsx
atom@atom-VirtualBox:~/htb/redeemer$ nmap -p1-10000 10.129.145.0
Starting Nmap 7.80 ( https://nmap.org ) at 2022-10-10 17:00 IST
Stats: 0:03:24 elapsed; 0 hosts completed (1 up), 1 undergoing Connect Scan
Connect Scan Timing: About 57.69% done; ETC: 17:06 (0:02:30 remaining)
Stats: 0:07:55 elapsed; 0 hosts completed (1 up), 1 undergoing Connect Scan
Connect Scan Timing: About 90.21% done; ETC: 17:09 (0:00:52 remaining)
Nmap scan report for 10.129.145.0
Host is up (0.19s latency).
Not shown: 9999 closed ports
PORT     STATE SERVICE
6379/tcp open  redis

Nmap done: 1 IP address (1 host up) scanned in 651.16 seconds
```

Databases are a collection of organized information that can be easily accessed, managed and updated. 

There are different types of databases and one among them is Redis, which is an 'in-memory' database. In - memory databases are the ones that rely essentially on the primary memory for data storage (meaning that the database is managed in the RAM of the system); in contrast to databases that store data on the disk or SSDs. As the primary memory is significantly faster than the secondary memory, the data retrieval time in the case of 'in-memory' databases is very small, thus offering very efficient & minimal response times.

```jsx
atom@atom-VirtualBox:~/htb/redeemer$ nmap -sC -sV -p 6379 10.129.145.0
Starting Nmap 7.80 ( https://nmap.org ) at 2022-10-10 18:07 IST
Nmap scan report for 10.129.145.0
Host is up (0.16s latency).

PORT     STATE SERVICE VERSION
6379/tcp open  redis   Redis key-value store 5.0.7

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.20 seconds
```

In-memory databases like Redis are typically used to cache data that is frequently requested for quick retrieval.  We can infer from the scan result that only one port is open on the target machine, i.e. port 6379 which is running a Redis server

Redis (REmote DIctionary Server) is an open-source advanced NoSQL key-value data store used as a database, cache, and message broker. The data is stored in a dictionary format having key-value pairs. It is typically used for short term storage of data that needs fast retrieval. Redis does backup data to hard drives to provide consistency

Redis runs as server-side software so its core functionality is in its server component. The server listens for connections from clients, programmatically or through the command-line interface. The database is stored in the server's RAM to enable fast data access. Redis also writes the contents of the database to disk at varying intervals to persist it as a backup, in case of failure.

In order to work with redis you have to install  ``` redis-cli ```  type this command to install it ``` sudo apt install redis-tools ```

```jsx
atom@atom-VirtualBox:~/htb/dancing$ sudo apt install redis-tools
[sudo] password for atom: 
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following packages were automatically installed and are no longer required:
  chromium-codecs-ffmpeg-extra gstreamer1.0-vaapi libgstreamer-plugins-bad1.0-0 libva-wayland2
Use 'sudo apt autoremove' to remove them.
The following additional packages will be installed:
  libatomic1 libhiredis0.14 libjemalloc2 liblua5.1-0 lua-bitop lua-cjson
Suggested packages:
  ruby-redis
The following NEW packages will be installed:
  libatomic1 libhiredis0.14 libjemalloc2 liblua5.1-0 lua-bitop lua-cjson redis-tools
0 upgraded, 7 newly installed, 0 to remove and 0 not upgraded.
Need to get 887 kB of archives.
After this operation, 3,955 kB of additional disk space will be used.
Do you want to continue? [Y/n]
```

``` info ```  command is used to for gathering information 

```jsx
atom@atom-VirtualBox:~/htb/dancing$ redis-cli -h 10.129.136.187
10.129.136.187:6379> info
# Server
redis_version:5.0.7
redis_git_sha1:00000000
redis_git_dirty:0
redis_build_id:66bd629f924ac924
redis_mode:standalone
os:Linux 5.4.0-77-generic x86_64
arch_bits:64
```

``` keys * ``` used to list the keys 

```jsx
10.129.136.187:6379> keys *
1) "flag"
2) "numb"
3) "temp"
4) "stor"
```

``` get ``` command is used to view content of the flag 

```jsx
atom@atom-VirtualBox:~/htb/redeemer$ redis-cli -h 10.129.145.83
10.129.145.83:6379> get flag
```

```bash
10.129.136.187:6379> get flag
"03e1d2b---------9922053953eb"
```