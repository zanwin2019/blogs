---
title: "warning: setlocale: LC_CTYPE: cannot change locale (UTF-8): 没有那个文件或目录"
date: 2019-02-14 14:26:13
tags: Linux
categories: Linux
---

SSH登陆linux服务器，显示
>警告: setlocale: LC_CTYPE: 无法改变区域选项 (UTF-8)（warning: setlocale: LC_CTYPE: cannot change locale (UTF-8): No such file or directory）。   

解决方案
在```/etc/environment```加入如下代码（如果没有该文件，需要新建）：
```
LC_ALL=zh_CN.UTF_8
LANG=zh_CN.UTF_8  
```
下次SSH登陆的时候，就不会出现如上警告了。