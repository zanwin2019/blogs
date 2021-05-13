---
title: Docker安装部署GitBook
date: 2019-05-06 13:57:40
tags: Docker
categories: Docker
---

# GitBook简介
GitBook 是一个基于 Node.js 的命令行工具，可使用 Github/Git 和 Markdown 来制作精美的电子书，GitBook 并非关于 Git 的教程。

GitBook支持输出多种文档格式：

- 静态站点：GitBook默认输出该种格式，生成的静态站点可直接托管搭载Github Pages服务上；
- PDF：需要安装gitbook-pdf依赖；
- eBook：需要安装ebook-convert；
- 单HTML网页：支持将内容输出为单页的HTML，不过一般用在将电子书格式转换为PDF或eBook的中间过程；
- JSON：一般用于电子书的调试或元数据提取。

# GitBook必备文件
使用GitBook制作电子书，必备两个文件：README.md和SUMMARY.md。

# Docker安装GitBook

### 搜索GitBook镜像
```
[root@devops02 ~]# docker search --filter=stars=3  gitbook
NAME                DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
fellah/gitbook      GitBook                                         33                                      [OK]
billryan/gitbook    Docker for GitBook with Unicode support         14                                      [OK]
gitbook/nuts        Releases/downloads server with auto-updater …   5
```

### 安装GitBook镜像
> 官方镜像下载太慢的话可以使用国内的daocloud

```
[root@devops02 ~]# docker pull fellah/gitbook
```

### 生成GitBook静态html文件
> /my_path下需要有README.md和SUMMARY.md两个文件。

```
[root@devops02 ~]# docker run -v /home/book:/srv/gitbook -v /home/book/html:/srv/html fellah/gitbook gitbook build . /srv/html
```

# 用nginx做web服务来展示book

### 搜索nginx镜像  

```
[root@devops02 ~]# docker search --filter=stars=3 nginx 
```

### 安装nginx镜像  

```
[root@devops02 ~]# docker run --name book -v /home/book/html:/usr/share/nginx/html -d -p 8080:80 nginx
```


# Gitlab-CI配置pipline
> 配置CI流程后，只需要提交代码到仓库，就可以自动编译部署。由于在一台服务器上，用了cp命令部署。如果gitlab-runner和gitbook项目在不同服务器，需用ssh和scp进行部署。  

```
before_script:
  # 配置环境
  - LANG="zh_CN.utf8"
  - export LC_ALL=zh_CN.UTF-8

stages:
  - deploy
  - build

# deploy
deploy:
  variables:
    CI_REPOSITORY_URL:
      http://$GIT_USERNAME:$GIT_PASSWORD@git.aioper.cn/$CI_PROJECT_PATH.git
  stage: deploy
  script:
    - echo "start deploy"
    - rm -rf $CI_PROJECT_NAME
    - echo "git version" && git version
    - git config --global user.email $GIT_EMAIL
    - git clone $CI_REPOSITORY_URL
    - \cp -r $CI_PROJECT_NAME/* $BOOK_PATH
    - echo "end deploy"


# gitbook build 
gitbook-build:
  stage: build
  script:
    - echo "start gitbook build"
    - docker -v
    - docker run -v /home/book:/srv/gitbook -v /home/book/html:/srv/html fellah/gitbook gitbook build . /srv/html
    - echo "end gitbook build"
  only:
    - master

```

