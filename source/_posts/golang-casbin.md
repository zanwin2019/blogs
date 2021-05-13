---
title: golang-casbin
date: 2019-09-02 14:32:18
tags: Golang
categories: 权限
---

# 简介
casbin采用的是PERM模型来设计的。一个模型conf至少有四个部分 [request_definition], [policy_definition], [policy_effect], [matchers]如果模型使用RBAC，它也应该添加[role_definition]。

# 请求定义
[request_definition]是访问请求的定义。它定义了函数中的参数e.Enforce(...)

```
[request_definition]
r = sub, obj, act
```

sub, obj, act,访问角色（Subject），访问的资源(Object) ，动作（Action）也可以叫访问的方法其实就是http方法GET POST那些

# 策略定义
其实就是定义了策略的规则。

```
[policy_definition]
p = sub, obj, act
p2 = sub, act
```

举个例子

```
p, alice, data1, read
p2, bob, write-all-objects
```

角色alice对path为data1有读的权限，角色bob可以对所有资源有wirte的权限。大概是这个意思。

# 策略效果
[policy_effect]是策略效果的定义。它定义了在多个策略规则与请求匹配时是否应批准访问请求。例如，一个规则允许而另一个规则否认。

```
[policy_effect]
e = some(where (p.eft == allow))
```

p.eft它可以是allow或deny，它是可选的，默认是allow，这里我也没理解很透彻。所以没办法说得很清楚。大家可以研究研究。。。另一个例子

```
[policy_effect]
e = !some(where (p.eft == deny))
```

# 匹配器
[matchers]是策略匹配的定义。匹配器是表达式。它定义了如何根据请求评估策略规则。这里的语法就是正则表达式。

```
[matchers]
m = r.sub == p.sub && r.obj == p.obj && r.act == p.act
```

关于这个模型真的不是一两句能说完的。还是去官方看文档比我说来的快一点。。我们直接写项目来理解。
首先我们go get github.com/casbin/casbin 获取下这个包。根据说明我们要准备conf 文件。官方有个在线的editor，你可以根据选项生成conf ，因为我写的是restfulapi，所以我们采用restful的conf，把这个文件放到conf文件夹下面，给个名字auth_model.conf。

```
[request_definition]
r = sub, obj, act

[policy_definition]
p = sub, obj, act

[policy_effect]
e = some(where (p.eft == allow))

[matchers]
m = r.sub == p.sub && keyMatch(r.obj, p.obj) && regexMatch(r.act, p.act)
```
