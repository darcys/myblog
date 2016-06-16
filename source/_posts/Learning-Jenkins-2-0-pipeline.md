title: Jenkins 2.0 pipeline 学习
date: 2016-06-02 14:47:53
categories:
 - Devops
tags: 
 - jenkins
---

### 问题
长久以来,jenkins不能通过code管理config都是个缺点,我说的code管理不仅仅是配置保存的git/svn,而是每个jenkins的配置都能先进git/svn,
再有个jenkins的功能能获取config的更新,自动更新jenkins的job,这样jenkins的管理也就可控了,任何变更都可review和追溯,做到真正的code as

### 目的
大概简单试用了一下Jenkins 2.0的pipeline功能, 看了jenkinsfile和一些dsl的介绍,比想象中的功能要强很多.
所以想系统的学习一下,看看没有可能通过jenkinsfile直接替换job config的定义.

