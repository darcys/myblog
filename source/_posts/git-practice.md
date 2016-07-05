title: git practice - 当devel branch和release branch不能完全merge
date: 2016-06-17 14:40:15
tags:
 - git
 - gitlab
categories:
 - Devops
---
<!-- toc -->

## 问题描述
要从svn转一个项目到git,这个项目有近4年历史, team members大概有20多人, codebase很大,最终生成一个war包.
开发模式,有个dev的branch,所有的功能都往这里放,然后有个出release的branch,dev branch完成测试的就手动merge到release的branch.
所以dev branch和release branch无法直接merge

<!-- more -->

## 我们team的使用情况
我们team人比较少,5个人,所以比较好协调,用的是标准的gitlab flow, 如果需要做feature,就从master上新建一个branch,然后做feature, 
feature的branch会做包测试,这个环节要求比较严格,必须测试通过,有了相应的task cases,然后才能提交merge request, code review, accept,merge 到master上.
所以merge完的基本就是可以进release的code. 也是得益于人比较少,所以可以在需要测试时协调一下,把自己的branch deploy上去进行测试.
并且真正release的版本只是commit指针落后一点的,指到master上的一个tag,所以相对简单很多.

## 借鉴ansible的项目的经验
因为ansible项目关注的比较多,https://github.com/ansible/ansible 
所以着重看了看. ansible和我们这个需要转的项目很类似, 重点关注devel branch和stable-2.1 branch:
- devel branch
    - Patches should always be made against the devel branch.
    - 所以开发者贡献代码,基本是从这个branch Fork一下, 做完feature后提交pr, merge进来.
    
- stable branch
    - 需要release的new feature或者一些bug fix, 会被cherry pick到stable上
    
ansible对提交pr也有要求,必须要先rebase一下,如果有多于的merge request,会被打回
>In order to keep the history clean and better audit incoming code, we will require resubmission of pull requests that contain merge commits. Use git pull --rebase (rather than git pull) and git rebase (rather than git merge). Also be sure to use topic branches to keep your additions on different branches, such that they won’t pick up stray commits later.

## gitlab里的实践
- gitlab对cherrypick的支持非常流程,比如,当在devel上提交了merge request之后,当master同意了这个merge request并完成merge,可以直接在页面操作cherrypick,选择branch stable,
这时会生成一个新的branch,叫cherrypick-xxx,同时会有一个新的merge request,merge branch cherrypick-xxx 到stable

- merge request之后就有cherry pick的button
![merge](http://7xk2xu.com1.z0.glb.clouddn.com/1.jpg)

- 点击cherry pick之后,可以选择一个branch进行cherry pick
![cp](http://7xk2xu.com1.z0.glb.clouddn.com/2.jpg)

- cherry pick会生成一个新的branch,并且有相应的merge request
![cp-res](http://7xk2xu.com1.z0.glb.clouddn.com/3.jpg)

同时gitlab也支持直接在页面选择一个commit进行cherry pick


## Runtime switches
如果想避免dev和stable差距过大的尴尬,还可以考虑引入Runtime switches,也就是说所有的代码都进到 main branch, 但不够稳定的, 不成熟的代码通过 runtime switch 来禁用.
下面还提到了为什么compile-time switch不好,如果有regression test的ta,又比较保守,可以考虑这种方式.设计得当可以解决大部分问题.
https://medium.com/@aboodman/in-march-2011-i-drafted-an-article-explaining-how-the-team-responsible-for-google-chrome-ships-c479ba623a1b#.uriu5haz2

## 综述
对于server来说,因为一定要考虑向后兼容,所以从持续集成的角度来说,一个feature做好,并通过当前的regression测试,应该就可以上产品线.
但是这有个前提条件,需要完整的自动化regression测试,否则测试工作量太大.因此在不具备这个条件时,最简单的方式就是尽量少放代码到下一个release.

