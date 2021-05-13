---
title: 解决rpm conflicts with file from package问题
date: 2019-04-26 10:34:29
tags:
  - Linux
  - Python
categories: 
  - Python
  - Linux
---

```
[root@harbor opt]# rpm -ivh python27-2.7.9-1.x86_64.rpm
准备中...                          ################################# [100%]
	file /usr/bin/python2.7 from install of python27-2.7.9-1.x86_64 conflicts with file from package python-2.7.5-76.el7.x86_64
	file /usr/share/man/man1/python2.7.1.gz from install of python27-2.7.9-1.x86_64 conflicts with file from package python-2.7.5-76.el7.x86_64
	file /usr/bin/python2.7-config from install of python27-2.7.9-1.x86_64 conflicts with file from package python-devel-2.7.5-76.el7.x86_64
	file /usr/include/python2.7/pyconfig.h from install of python27-2.7.9-1.x86_64 conflicts with file from package python-devel-2.7.5-76.el7.x86_64
```

# 方法一
yum -y remove python-2.7.5-76.el7.x86_64
卸载掉冲突的文件，安装新的文件。如果由于由于依赖关系导致要卸载很多软件，那可以优先考虑下一个方法。
# 方法二
rpm -ivh python27-2.7.9-1.x86_64.rpm --replacefiles
安装的时候增加--replacefiles参数，但是不知道在yum里如何实现。

> --replacepkgs 强制重新安装已经安装的软件包 
--replacefiles 替换属于其它软件包的文件
rpm -e --nodeps python27-2.7.9-1.x86_64.rpm 强制删除包