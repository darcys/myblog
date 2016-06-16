title: 美化自己的命令行环境，增加漂亮的状态行，安装使用powerline
date: 2015-09-12 15:43:54
categories:
 - Tech for Fun
tags:
 - coding for fun
 - powerline
 
---
>Better to do something imperfectly than to do nothing flawlessly.
>做事不完美，总比完美地不做事要好。
>>罗伯特·舒勒


### 介绍

首先大概介绍下powerline, 作为插件，可以用在bash,zsh,vim，tmux等等上，然后就会有很漂亮的状态栏

#### zsh里的效果
![](http://7xk2xu.com1.z0.glb.clouddn.com/20150911Snip20150911_5.png)

#### vim效果
![](http://7xk2xu.com1.z0.glb.clouddn.com/20150911Snip20150911_1.png)
![](http://7xk2xu.com1.z0.glb.clouddn.com/20150911Snip20150911_2.png)
![](http://7xk2xu.com1.z0.glb.clouddn.com/20150911Snip20150911_3.png)
![](http://7xk2xu.com1.z0.glb.clouddn.com/20150911Snip20150911_4.png)

### 安装使用

#### 安装
{% codeblock lang:zsh %}
sudo pip install powerline-status
{% endcodeblock %}

#### 使用
详情可以参与官方文档
https://powerline.readthedocs.org/en/master/


#### 取到自己的repository_root，后面配置都要将repository_root替换成自己的Location
{% codeblock lang:zsh %}
pip show powerline-status                                             
---
Name: powerline-status
Version: 2.2
Location: /Library/Python/2.7/site-packages
Requires:
{% endcodeblock %}

#### vim
vim 有些需求：
https://powerline.readthedocs.org/en/master/usage.html#vim-plugin-requirements

在vimrc里加一行即可
{% codeblock lang:vim %}
set rtp+={repository_root}/powerline/bindings/vim
{% endcodeblock %}

#### zsh
在zshrc里加一行
{% codeblock lang:vim %}
. {repository_root}/powerline/bindings/zsh/powerline.zsh
{% endcodeblock %}


#### trouble shooting
##### 如果遇到python locale的错误，把下面两行加到/etc/bashrc里
{% codeblock lang:shell %}
export LC_ALL=en_US.UTF-8
export LANG=en_US.UTF-8
{% endcodeblock %}


##### 如果箭头显示不正常，需要安装一下powerline的font
请参考：
{% post_link powerline-font %}
