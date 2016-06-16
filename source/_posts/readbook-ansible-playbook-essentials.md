title: 阅读笔记- 《Ansible Playbook Essentials》
date: 2015-09-04 14:16:28
categories:
 - 读书笔记-技术类
tags: 
 - reading-notes
 - ansible
---

### 简介
个人觉得在阅读这本书前，最好读一些基础的ansible书籍，看过一些例子，然后自己写过一些ansible的playbook，对playbook有初步的了解，同时问题也有一些。
这本书是着重阐述怎么去设计一个结构良好，功能完善的playbook，内容还是不错的，官方文档或者入门书籍着重介绍ansible的各个功能，但是对如果组织使用这些功能探讨的不会太深。
再没看这本书之前，也许你和我一样，写出的playbook的role都只有main.yml文件，这本书是指导我们应该怎么样正确的规划我们的infrastructure，从而然后写出可复用的，模块化的playbook

### 读后感
这本书从一个简单的例子，最直观的方式完成一个playbook，后续的章节，围绕这个例子做各种各样的重构。所以前后一贯，再看到后面的章节的时会不断的对之前的做法进行反思。 有些pragmatic的风格。
此外，例子中的output截图配上注释非常直观，便于理解。
最喜欢的是第九章，因为自己刚写了没多久的playbook，也一直考虑多环境的playbook该如何设计，并且dev和prod不会互相影响，又方便merge code，我觉得第二种方案实在太爽了，这里就不书透了，如果有相同问题，可以找来重点看看。

### 笔记
* pre-tasks和post-tasks可以用来在执行之前关闭和打开nagios
* delegate_to可以用来做从lb摘除web server之类的动作
* 在playbook里指定serial参数用来限定每次执行的server个数
* wait_for用来检查状态很用帮助
* 将安装和配置分离，用main.yml import install.yml 和 configure.yml, 让task 模块化  
* 如果要用command/shell等模块，为了保持幂等性，可以使用creates和removes flag
    * 如果command会创建一个文件，可以用creates flag来测试这个文件，如果已经存在，则不再做
    * 如果command的用途不是创建文件，也需要创建一个flag用来保持幂等性。
* 变量定义, ansible可以定义变量的地方实在是很多，书中有一节梳理的非常清晰
* 多级变量需要merge的话，得打开hash_behaviour=merge，参见下面的例子

#### 多级变量 merging hashes
比如在default里定义了
{% codeblock lang:config %}
 redis:
    config:
       port: 6379
       url: xxxx.redis.xxxx.com
{% endcodeblock %}
在 vars里定义了
{% codeblock lang:config %}
redis:
   config:
      port: 4567
{% endcodeblock %}

按照ansible default的行为，最终会用vars的replace default的，所以结果是vars的
{% codeblock lang:config %}
redis:
   config:
      port: 4567
{% endcodeblock %}

如果想得到merge的结果
{% codeblock lang:config %}
 redis:
    config:
       port: 4567
       url: xxxx.redis.xxxx.com
{% endcodeblock %}
需要把配置改成
{% codeblock lang:config %}
# /etc/ansible/ansible.cfg
hash_behaviour=merge
{% endcodeblock %}

#### jinja2的if else写法
{% codeblock lang:jinjia2 %}
{/% if condition %/}
    do_some_thing
{/% elif condition2 /%}
    do_another_thing
{/% else /%}
    do_something_else
{/% endif /%}
{% endcodeblock %}
看了书里的例子，这个写法可读性比较差，实在搞不定再考虑这种写法吧。

#### 用ansible-galaxy 生成role的基本骨架，这样就省得自己去建role需要的各种目录了
{% codeblock lang:java %}
ansible-galaxy init --init-path roles/ dynamod
{% endcodeblock %}

{% codeblock lang:bash %}
dadeMacBook-Pro:zoom_xmpp darcy$ ansible-galaxy init --init-path roles/ dynamodb
- dynamodb was created successfully

dadeMacBook-Pro:zoom_xmpp darcy$ cd roles/dynamodb/
dadeMacBook-Pro:dynamodb darcy$ ll -R
total 8
-rw-r--r--  1 darcy  staff  1328 Sep  4 15:44 README.md
drwxr-xr-x  3 darcy  staff   102 Sep  4 15:44 defaults
drwxr-xr-x  2 darcy  staff    68 Sep  4 15:44 files
drwxr-xr-x  3 darcy  staff   102 Sep  4 15:44 handlers
drwxr-xr-x  3 darcy  staff   102 Sep  4 15:44 meta
drwxr-xr-x  3 darcy  staff   102 Sep  4 15:44 tasks
drwxr-xr-x  2 darcy  staff    68 Sep  4 15:44 templates
drwxr-xr-x  3 darcy  staff   102 Sep  4 15:44 vars

./defaults:
total 8
-rw-r--r--  1 darcy  staff  33 Sep  4 15:44 main.yml

./files:

./handlers:
total 8
-rw-r--r--  1 darcy  staff  33 Sep  4 15:44 main.yml

./meta:
total 8
-rw-r--r--  1 darcy  staff  2373 Sep  4 15:44 main.yml

./tasks:
total 8
-rw-r--r--  1 darcy  staff  30 Sep  4 15:44 main.yml

./templates:

./vars:
total 8
-rw-r--r--  1 darcy  staff  29 Sep  4 15:44 main.yml
{% endcodeblock %}
