---
title: python的猴子补丁monkey patch
date: 2018-12-12 10:50:08
tags: Python
categories: Python
---

monkey patch指的是在运行时动态替换,一般是在startup的时候.

用过gevent就会知道,会在最开头的地方gevent.monkey.patch_all();把标准库中的thread/socket等给替换掉.这样我们在后面使用socket的时候可以跟平常一样使用,无需修改任何代码,但是它变成非阻塞的了.

一个比较实用的例子，很多代码用到 import json，后来发现ujson性能更高，如果觉得把每个文件的import json 改成 import ujson as json成本较高，或者说想测试一下用ujson替换json是否符合预期，只需要在入口加上：

```
import json 
import ujson 

def monkey_patch_json(): 
    json.__name__ = 'ujson' 
    json.dumps = ujson.dumps 
    json.loads = ujson.loads 
 
monkey_patch_json()
``` 

最后,注意不能单纯的json = ujson来替换.