---
title: Gerrit安装部署
date: 2019-02-22 10:06:52
tags: Git
categories: Git
---

## Gerrit简介
>Gerrit，一种免费、开放源代码的代码审查软件，使用网页界面。利用网页浏览器，同一个团队的软件程序员，可以相互审阅彼此修改后的程序代码，决定是否能够提交，退回或者继续修改。  

## 一、创建gerrit用户
```
[root@devops02 ~]# adduser gerrit -m
You have mail in /var/spool/mail/root
[root@devops02 ~]# passwd gerrit
Changing password for user gerrit.
New password:
BAD PASSWORD: The password is shorter than 8 characters
Retype new password:
passwd: all authentication tokens updated successfully.
[root@devops02 ~]#
```

## 二、安装gerrit
```
[root@devops02 ~]# su - gerrit
Last login: Wed Feb 20 15:05:42 CST 2019 on pts/0
[gerrit@devops02 ~]$ pwd
/home/gerrit
[gerrit@devops02 ~]$ ll
total 72520
-rw-r--r--. 1 root root 74258227 Feb 20 15:09 gerrit-2.16.5.war
[gerrit@devops02 ~]$ java -jar gerrit-2.16.5.war init -d ~/gerrit_site
```

## 三、安装步骤
```
Using secure store: com.google.gerrit.server.securestore.DefaultSecureStore
[2019-02-20 15:16:04,189] [main] INFO  com.google.gerrit.server.config.GerritServerConfigProvider : No /home/gerrit/gerrit_site/etc/gerrit.config; assuming defaults

*** Gerrit Code Review 2.16.5
***


*** Git Repositories
***

Location of Git repositories   [git]:

*** SQL Database
***

Database server type           [h2]:

*** Index
***

Type                           [lucene/?]:

*** User Authentication
***

Authentication method          [openid/?]: http
Get username from custom HTTP header [y/N]?
SSO logout URL                 :
Enable signed push support     [y/N]?

*** Review Labels
***

Install Verified label         [y/N]?

*** Email Delivery
***

SMTP server hostname           [localhost]:
SMTP server port               [(default)]:
SMTP encryption                [none/?]:
SMTP username                  :

*** Container Process
***

Run as                         [gerrit]:
Java runtime                   [/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.102-4.b14.el7.x86_64/jre]:
Copy gerrit-2.16.5.war to /home/gerrit/gerrit_site/bin/gerrit.war [Y/n]?
Copying gerrit-2.16.5.war to /home/gerrit/gerrit_site/bin/gerrit.war

*** SSH Daemon
***

Listen on address              [*]:
Listen on port                 [29418]:
Generating SSH host key ... rsa... ed25519... ecdsa 256... ecdsa 384... ecdsa 521... done

*** HTTP Daemon
***

Behind reverse proxy           [y/N]?
Use SSL (https://)             [y/N]?
Listen on address              [*]:
Listen on port                 [8080]: 8081
Canonical URL                  [http://devops02:8081/]:

*** Cache
***


*** Plugins
***

Installing plugins.
Install plugin codemirror-editor version v2.16.5 [y/N]?
Install plugin commit-message-length-validator version v2.16.5 [y/N]?
Install plugin download-commands version v2.16.5 [y/N]?
Install plugin hooks version v2.16.5 [y/N]?
Install plugin replication version v2.16.5 [y/N]?
Install plugin reviewnotes version v2.16.5 [y/N]?
Install plugin singleusergroup version v2.16.5 [y/N]?
Initializing plugins.
No plugins found with init steps.

Initialized /home/gerrit/gerrit_site
Init complete, reindexing projects with: reindex --site-path /home/gerrit/gerrit_site --threads 1 --iReindexing projects:    100% (2/2)
Reindexed 2 documents in projects index in 0.1s (38.5/s)
```

## 四、nginx安装配置

## 五、创建登陆认证文件
```
[gerrit@devops02 ~]$ htpasswd -c /home/gerrit/gerrit.password admin
New password:
Re-type new password:
Adding password for user admin
[gerrit@devops02 ~]$
```
htpasswd是apache的一个模块，需要先安装apache服务，通过以下任意一个安装
```
root@devops02 ~]# yum install httpd-tools //单独安装部分工具
```
```
[root@devops02 ~]# yum install httpd //安装完整的apache
```

## 六、设置权限
```
[gerrit@devops02 ~]$ cd /home/
[gerrit@devops02 home]$ chmod 755 gerrit/
```

## 七、配置gerrit.config
```
[gerrit@devops02 home]$ cd /home/gerrit/gerrit_site/etc/
[gerrit@devops02 etc]$ ll
total 52
-rw-rw-r--. 1 gerrit gerrit  716 Feb 20 15:22 gerrit.config
drwxrwxr-x. 2 gerrit gerrit 4096 Feb 20 15:22 mail
-rw-------. 1 gerrit gerrit   71 Feb 20 15:17 secure.config
-rw-------. 1 gerrit gerrit  288 Feb 20 15:18 ssh_host_ecdsa_384_key
-rw-r--r--. 1 gerrit gerrit  233 Feb 20 15:18 ssh_host_ecdsa_384_key.pub
-rw-------. 1 gerrit gerrit  365 Feb 20 15:18 ssh_host_ecdsa_521_key
-rw-r--r--. 1 gerrit gerrit  281 Feb 20 15:18 ssh_host_ecdsa_521_key.pub
-rw-------. 1 gerrit gerrit  227 Feb 20 15:18 ssh_host_ecdsa_key
-rw-r--r--. 1 gerrit gerrit  189 Feb 20 15:18 ssh_host_ecdsa_key.pub
-rw-------. 1 gerrit gerrit  419 Feb 20 15:18 ssh_host_ed25519_key
-rw-r--r--. 1 gerrit gerrit  109 Feb 20 15:18 ssh_host_ed25519_key.pub
-rw-------. 1 gerrit gerrit 1675 Feb 20 15:18 ssh_host_rsa_key
-rw-r--r--. 1 gerrit gerrit  409 Feb 20 15:18 ssh_host_rsa_key.pub
[gerrit@devops02 etc]$ vi gerrit.config
```
```
[gerrit]
        basePath = git
        serverId = 280a982e-5d60-49c4-95f6-0b944f4b1c5e
        canonicalWebUrl = http://192.168.51.36:81/
[database]
        type = h2
        database = /home/gerrit/gerrit_site/db/ReviewDB
[container]
        javaOptions = "-Dflogger.backend_factory=com.google.common.flogger.backend.log4j.Log4jBackendFactory#getInstance"
        javaOptions = "-Dflogger.logging_context=com.google.gerrit.server.logging.LoggingContext#getInstance"
        user = gerrit
        javaHome = /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.102-4.b14.el7.x86_64/jre
[index]
        type = LUCENE
[auth]
        type = HTTP
[oauth]
        allowEditFullName = true
        allowRegisterNewEmail = true
[receive]
        enableSignedPush = false
[sendemail]
        enable = false
[sshd]
        listenAddress = *:29418
[httpd]
        listenUrl = http://*:8081/
[cache]
        directory = cache
```

## 八、重启gerrit
```
[gerrit@devops02 etc]$ /home/gerrit/gerrit_site/bin/gerrit.sh restart
Stopping Gerrit Code Review: OK
Starting Gerrit Code Review: OK

```
## 九、启动nginx
```
[root@devops02 ~]# /usr/local/nginx/sbin/nginx
```
## 十、访问
```
http://192.168.81.163:81 //根据实际情况修改IP和端口
```