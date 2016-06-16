title: reading-notes-Creating-Development-Environments-with-Vagrant
date: 2016-06-02 15:35:53
categories:
 - 读书笔记-技术类
tags:
  - reading-notes
  - Vagrant
---


### 初衷
写了一些ansible,docker相关的代码,经常想本地调试一下,尤其希望能有一个标准的干净的环境能测试.
之前对Vagrant稍有了解,借这次机会好好学习一下.
最终目标是希望能一个setup.sh把所有的脚本代码都黏在一块儿.launch一套完整的系统.

### 这句话挺吸引人
> “New team members can be onboarded to new projects as easy as git clone && vagrant up” 
>  Excerpt From: Michael Peacock. “Creating Development Environments with Vagrant - Second Edition.” iBooks. 

### 阅读简单笔记
- install virtualbox
- install vagrant https://www.vagrantup.com/downloads.html
- vagrant init --force hashicorp/precise64 生成vagrantfile
- vagrant up 把precise64launch起来,因为本地没有box文件,所以远程下载
- https://atlas.hashicorp.com/boxes/search?utf8=✓&sort=&provider=virtualbox&q=centos 这里可以找到自己需要的box
- 我需要centos 7.1,所以按照此页面https://atlas.hashicorp.com/bento/boxes/centos-7.1, 执行 vagrant init bento/centos-7.1; vagrant up --provider parallels


