title: ejabberd ver 16 makefile 代码阅读笔记
date: 2016-07-04 09:47:28
tags:
	- ejabberd
---

<!-- toc -->
### ejabberd 14.07 和 16.04 makefile的区别
#### ejabberd 14.07 (3d3a4f7)
https://github.com/processone/ejabberd/blob/3d3a4f75435ad353cf9a192b8ef23335be0a5b01/Makefile.in

#### ejabberd 16.04 (639c2fb)
https://github.com/processone/ejabberd/blob/639c2fb6401391663206c0e4c946d1a699689ac7/Makefile.in

#### 区别最大的就是下面这一段
下面这一段比之前的用法要复杂，正好可以借这个机会把makefile更好的理解一下
{% codeblock lang:zsh %}
JOIN_PATHS=$(if $(wordlist 2,1000,$(1)),$(firstword $(1))/$(call JOIN_PATHS,$(wordlist 2,1000,$(1))),$(1))

VERSIONED_DEP=$(if $(DEP_$(1)_VERSION),$(DEP_$(1)_VERSION),$(1))

ELIXIR_TO_DEST=$(LIBDIR) $(call VERSIONED_DEP,$(word 2,$(1))) $(wordlist 5,1000,$(1))
DEPS_TO_DEST=$(LIBDIR) $(call VERSIONED_DEP,$(word 2,$(1))) $(wordlist 3,1000,$(1))
MAIN_TO_DEST=$(LIBDIR) $(call VERSIONED_DEP,ejabberd) $(1)
TO_DEST_SINGLE=$(if $(subst XdepsX,,X$(word 1,$(1))X),$(call MAIN_TO_DEST,$(1)),$(if $(subst XlibX,,X$(word 3,$(1))X),$(call DEPS_TO_DEST,$(1)),$(call ELIXIR_TO_DEST,$(1))))
TO_DEST=$(foreach path,$(1),$(call JOIN_PATHS,$(call TO_DEST_SINGLE,$(subst /, ,$(path)))))

FILTER_DIRS=$(foreach path,$(1),$(if $(wildcard $(path)/*),,$(path)))
FILES_WILDCARD=$(call FILTER_DIRS,$(foreach w,$(1),$(wildcard $(w))))

ifeq ($(MAKECMDGOALS),copy-files-sub)

DEPS:=$(sort $(shell $(REBAR) list-deps|$(SED) -e '/^=/d;s/ .*//'))

DEPS_FILES=$(call FILES_WILDCARD,$(foreach DEP,$(DEPS),deps/$(DEP)/ebin/*.beam deps/$(DEP)/ebin/*.app deps/$(DEP)/priv/* deps/$(DEP)/priv/lib/* deps/$(DEP)/priv/bin/* deps/$(DEP)/include/*.hrl deps/$(DEP)/lib/*/ebin/*.beam deps/$(DEP)/lib/*/ebin/*.app))
DEPS_FILES_FILTERED=$(filter-out %/epam deps/elixir/ebin/elixir.app,$(DEPS_FILES))
DEPS_DIRS=$(sort deps/ $(foreach DEP,$(DEPS),deps/$(DEP)/) $(dir $(DEPS_FILES)))

MAIN_FILES=$(filter-out %/configure.beam,$(call FILES_WILDCARD,ebin/*.beam ebin/*.app priv/msgs/*.msg priv/lib/* include/*.hrl))
MAIN_DIRS=$(sort $(dir $(MAIN_FILES)) priv/bin priv/sql)

define DEP_VERSION_template
DEP_$(1)_VERSION:=$(shell $(SED) -e '/vsn/!d;s/.*, *"/$(1)-/;s/".*//' $(2) 2>/dev/null)
endef

$(foreach DEP,$(DEPS),$(eval $(call DEP_VERSION_template,$(DEP),deps/$(DEP)/ebin/$(DEP).app)))
$(eval $(call DEP_VERSION_template,ejabberd,ebin/ejabberd.app))

define COPY_template
$(call TO_DEST,$(1)): $(1) $(call TO_DEST,$(dir $(1))) ; $$(INSTALL) -m 644 $(1) $(call TO_DEST,$(1))
endef

$(foreach file,$(DEPS_FILES_FILTERED) $(MAIN_FILES),$(eval $(call COPY_template,$(file))))

$(sort $(call TO_DEST,$(MAIN_DIRS) $(DEPS_DIRS))):
	$(INSTALL) -d $@

$(call TO_DEST,deps/p1_pam/priv/bin/epam): $(LIBDIR)/%: deps/p1_pam/priv/bin/epam $(call TO_DEST,deps/p1_pam/priv/bin/)
	$(INSTALL) -m 750 $(O_USER) $< $@

$(call TO_DEST,priv/sql/lite.sql): sql/lite.sql $(call TO_DEST,priv/sql)
	$(INSTALL) -m 644 $< $@

$(call TO_DEST,priv/bin/captcha.sh): tools/captcha.sh $(call TO_DEST,priv/bin)
	$(INSTALL) -m 750 $(O_USER) $< $@

copy-files-sub2: $(call TO_DEST,$(DEPS_FILES) $(MAIN_FILES) priv/bin/captcha.sh priv/sql/lite.sql)

endif

copy-files:
	$(MAKE) copy-files-sub

copy-files-sub: copy-files-sub2

install: all copy-files
{% endcodeblock %}
<!-- more -->


### 最近的变更
其实之前改makefile都是照猫画虎，改动较小，就模仿一下能work就行了。
这次16.04的改动比较大，而且看不太懂，故此仔细理解一下。

#### makefile reference
https://www.gnu.org/software/make/manual/
首先过了一遍手册详细理解了一下makefile的语法

#### 关于makefile的call
https://www.gnu.org/software/make/manual/html_node/Call-Function.html
$(call variable,param,param,…)

注意call函数后面第一个参数是个变量，后面是给变量再传递的函数。

### 这段新代码的入口
我们看看入口：
>copy-files-sub: copy-files-sub2
>copy-files-sub2: $(call TO_DEST,$(DEPS_FILES) $(MAIN_FILES) priv/bin/captcha.sh priv/sql/lite.sql)
copy-files-sub2是copy-files-sub的prerequisite
copy-files-sub2是个target，：后面的是prerequisites，我们在讨论make install的执行过程前，先理解一下后面这个call的语句。
这里是call了 TO_DEST这个变量，并将$(DEPS_FILES) $(MAIN_FILES) priv/bin/captcha.sh priv/sql/lite.sql 传给这个变量

### 理解 $(call TO_DEST,$(DEPS_FILES) $(MAIN_FILES) priv/bin/captcha.sh priv/sql/lite.sql)
再来看看TO_DEST的定义
TO_DEST=$(foreach path,$(1),$(call JOIN_PATHS,$(call TO_DEST_SINGLE,$(subst /, ,$(path)))))
这里$(1)就是$(DEPS_FILES) $(MAIN_FILES) priv/bin/captcha.sh priv/sql/lite.sql了

##### make的函数
foreach是make的一个函数
make的function可以在这里找到
https://www.gnu.org/software/make/manual/html_node/Functions.html#Functions

foreach function的文档在这个链接
https://www.gnu.org/software/make/manual/html_node/Foreach-Function.html#Foreach-Function

#### 赋值表达式
DEPS:=$(sort $(shell $(REBAR) list-deps|$(SED) -e '/^=/d;s/ .*//'))
TO_DEST=$(foreach path,$(1),$(call JOIN_PATHS,$(call TO_DEST_SINGLE,$(subst /, ,$(path)))))

注意这里DEPS使用:=赋值，而TO_DEST使用=赋值，具体有什么区别呢？
详细见这里
https://www.gnu.org/software/make/manual/html_node/Setting.html#Setting

>Variables defined with ‘=’ are recursively expanded variables. Variables defined with ‘:=’ or ‘::=’ are simply expanded variables; these definitions can contain variable references which will be expanded before the definition is made. See The Two Flavors of Variables.

recursively expanded variables: 简单的说，就是在定义时就确定好值了，和一般编程语言的用法一致
simply expanded variables： 只有在变量被使用时，才对这个变量里引用的其他变量取当前值。

如果实在无法理解，可以本地创建makefile，执行一下试试
{% codeblock lang:zsh %}
cat makefile
X = foo
Y = $(X) bar
X = notfoo

all:
	@echo $(Y)
 darcy /tmp  make        
notfoo bar
{% endcodeblock %}

{% codeblock lang:zsh %}
cat makefile
X := foo
Y := $(X) bar
X := notfoo

all:
	@echo $(Y)
 darcy  /tmp  make        
foo bar

{% endcodeblock %}
不过这里DEPS用:=还真是没看出什么特别之处，可能是防止在make里会添加其他运算，而这里需要最终值吧

#### 逻辑梳理
知道了大概的几个语法，我们来详细看看这段代码的作用

TO_DEST=$(foreach path,$(1),$(call JOIN_PATHS,$(call TO_DEST_SINGLE,$(subst /, ,$(path)))))
$(foreach `<var>`,`<list>`,`<text>`)
- 这里path是变量名，传递给后面的subst
- $(1) 是之前传入的paramlist: $(DEPS_FILES) $(MAIN_FILES) priv/bin/captcha.sh priv/sql/lite.sql priv/lib/zoom_cpu_stat.so
- text参数是另三个嵌套的表达式
-- $(subst `<from>`,`<to>`,`<text>`), 把字符串`<text>` 中的 `<from>` 替换为 `<to>` $(subst /, ,$(path)) , priv/bin/captcha.sh 会变成 priv bin captcha
-- call TO_DEST_SINGLE,$(subst /, ,$(path)) ,这里call了另一个TARGET TO_DEST_SINGLE，见下面介绍
-- JOIN_PATHS=$(if $(wordlist 2,1000,$(1)),$(firstword $(1))/$(call JOIN_PATHS,$(wordlist 2,1000,$(1))),$(1))
如果$(wordlist 2,1000,$(1))为true，则执行$(firstword $(1))/$(call JOIN_PATHS,$(wordlist 2,1000,$(1))),否则返回$(1) #$(if `<condition>`,`<then-part>`,`<else-part>`)
@echo $(wordlist 1,3,aa bb cc dd) # aa bb cc

if $(wordlist 2,1000,$(1)), 如果路径的深度大于1，比如priv bin，为true
后面是个递归调用，如果路径长度小于2，则直接返回，否则取路径第一个参数后面加个/
总之最终的效果就是把之前/替换成的空格，又换成了/. 为什么要这么写呢？感觉特别蠢，但是貌似暂时想不到特别好的办法，因为TO_DEST_SINGLE需要处理路径，比如替换路径名，但是word只支持空格分隔的字符串。

#### TO_DEST_SINGLE
TO_DEST_SINGLE=$(if $(subst XdepsX,,X$(word 1,$(1))X),$(call MAIN_TO_DEST,$(1)),$(if $(subst XlibX,,X$(word 3,$(1))X),$(call DEPS_TO_DEST,$(1)),$(call ELIXIR_TO_DEST,$(1))))
- $(word 1,$(1)) #@echo $(word 1,aa bb cc dd) 会显示aa，返回第一个单词
- $(subst XdepsX,,X$(word 1,$(1))X)， 如果路径第一个单词是deps, call MAIN_TO_DEST, 否则执行$(if $(subst XlibX,,X$(word 3,$(1))X),$(call DEPS_TO_DEST,$(1)),$(call ELIXIR_TO_DEST,$(1)))

现在看看true的逻辑：MAIN_TO_DEST=$(LIBDIR) $(call VERSIONED_DEP,ejabberd) $(1)
- VERSIONED_DEP=$(if $(DEP_$(1)_VERSION),$(DEP_$(1)_VERSION),$(1)) ... 这是什么鬼东西？
- DEP_$(1)_VERSION:=$(shell $(SED) -e '/vsn/!d;s/.*, *"/$(1)-/;s/".*//' $(2) 2>/dev/null) ...这里$(2)是啥  

#### VERSIONED_DEP， make template的定义
下面解释一下VERSIONED_DEP, 代码里定义了一个template
define DEP_VERSION_template
DEP_$(1)_VERSION:=$(shell $(SED) -e '/vsn/!d;s/.*, *"/$(1)-/;s/".*//' $(2) 2>/dev/null)
endef
然后紧随其后是两个运算这个template的语句
$(foreach DEP,$(DEPS),$(eval $(call DEP_VERSION_template,$(DEP),deps/$(DEP)/ebin/$(DEP).app)))
$(eval $(call DEP_VERSION_template,ejabberd,ebin/ejabberd.app))
效果就是定义出类似
DEP_ejabberd_VERSION:=$(shell $(SED) -e '/vsn/!d;s/.*, *"/ejabberd-/;s/".*//' ebin/ejabberd.app) 2>/dev/null)
的变量，
执行一下
{% codeblock lang:zsh %}
gsed  -e '/vsn/!d;s/.*, *"/ejabberd-/;s/".*//' ebin/ejabberd.app                                                           upgrade 
ejabberd-1.0.0
{% endcodeblock %}
这段代码都是好理解，不过有些不理解的是，template的这些语句，是什么时候执行的呢？其实这些语句是定义在copy-files-sub2之前的，在执行copy-files-sub2时，会先执行掉，所以这些值都是有的

#### DEP, DEPS_FILES
细说一下这里，$(1)的值是什么样的
首先看看入口的变量DEPS_FILES
DEPS_FILES=$(call FILES_WILDCARD,$(foreach DEP,$(DEPS),deps/$(DEP)/ebin/*.beam deps/$(DEP)/ebin/*.app deps/$(DEP)/priv/* deps/$(DEP)/priv/lib/* deps/$(DEP)/priv/bin/* deps/$(DEP)/include/*.hrl deps/$(DEP)/lib/*/ebin/*.beam deps/$(DEP)/lib/*/ebin/*.app))
foreach比较好理解，遍历DEPS，然后返回最后一堆路径给FILES_WILDCARD

DEPS:=$(sort $(shell $(REBAR) list-deps|$(SED) -e '/^=/d;s/ .*//'))
DEPS执行了一个shell，这里可以试着执行一下
{% codeblock lang:zsh %}
/usr/local/Cellar/erlang/18.3/lib/erlang/bin/escript rebar list-deps|/usr/local/bin/gsed -e '/^=/d;s/ .*//'                 upgra
goldrush
p1_utils
p1_utils
p1_utils
p1_utils
fast_tls
p1_utils
stun
fast_tls
p1_utils
p1_utils
proper
covertool
meck
jsx
eini
lhttpc
goldrush
lager
p1_utils
cache_tab
fast_tls
stringprep
fast_xml
stun
esip
fast_yaml
jiffy
p1_oauth2
p1_xmlrpc
luerl
proper
covertool
eini
jsx
lhttpc
erlcloud
eredis
ezlib
iconv
{% endcodeblock %}

总而言之会拿到所有rebar里的依赖。
所以就会返回比如deps/erlcloud/ebin/*.beam deps/erlcloud/ebin/*.app deps/erlcloud/priv/* 等给FILES_WILDCARD

FILES_WILDCARD=$(call FILTER_DIRS,$(foreach w,$(1),$(wildcard $(w))))
FILTER_DIRS=$(foreach path,$(1),$(if $(wildcard $(path)/*),,$(path)))
foreach这里会一个一个处理。比如deps/erlcloud/ebin/*.beam,会执行wildcard deps/erlcloud/ebin/*.beam, 得到wildcard对应的文件列表，然后通过FILTER_DIRS过滤掉目录。总之会拿到deps下面所需要的所有文件的列表

一小段测试代码方便理解，需要执行的话可以放到ejabberd的根目录
{% codeblock lang:zsh %}
cat Makefile                                                                                                                upgrade 
DEPS=jsx lager

DEPS_FILES=$(call FILES_WILDCARD,$(foreach DEP,$(DEPS),deps/$(DEP)/ebin/*.beam deps/$(DEP)/ebin/*.app deps/$(DEP)/priv/* deps/$(DEP)/priv/lib/* deps/$(DEP)/priv/bin/* deps/$(DEP)/include/*.hrl deps/$(DEP)/lib/*/ebin/*.beam deps/$(DEP)/lib/*/ebin/*.app))
FILES_WILDCARD=$(call FILTER_DIRS,$(foreach w,$(1),$(wildcard $(w))))
FILTER_DIRS=$(foreach path,$(1),$(if $(wildcard $(path)/*),,$(path)))

all:
	@echo $(DEPS_FILES)

make的结果
deps/jsx/ebin/jsx.beam deps/jsx/ebin/jsx_config.beam deps/jsx/ebin/jsx_consult.beam deps/jsx/ebin/jsx_decoder.beam deps/jsx/ebin/jsx_encoder.beam deps/jsx/ebin/jsx_parser.beam deps/jsx/ebin/jsx_to_json.beam deps/jsx/ebin/jsx_to_term.beam deps/jsx/ebin/jsx_verify.beam deps/jsx/ebin/jsx.app deps/lager/ebin/error_logger_lager_h.beam deps/lager/ebin/lager.beam deps/lager/ebin/lager_app.beam deps/lager/ebin/lager_backend_throttle.beam deps/lager/ebin/lager_common_test_backend.beam deps/lager/ebin/lager_config.beam deps/lager/ebin/lager_console_backend.beam deps/lager/ebin/lager_crash_log.beam deps/lager/ebin/lager_default_formatter.beam deps/lager/ebin/lager_file_backend.beam deps/lager/ebin/lager_format.beam deps/lager/ebin/lager_handler_watcher.beam deps/lager/ebin/lager_handler_watcher_sup.beam deps/lager/ebin/lager_msg.beam deps/lager/ebin/lager_stdlib.beam deps/lager/ebin/lager_sup.beam deps/lager/ebin/lager_transform.beam deps/lager/ebin/lager_trunc_io.beam deps/lager/ebin/lager_util.beam deps/lager/ebin/lager.app deps/lager/priv/edoc.css deps/lager/include/lager.hrl

{% endcodeblock %}

重新回到MAIN_TO_DEST=$(LIBDIR) $(call VERSIONED_DEP,ejabberd) $(1)，
$(1)就是处理后的 deps/jsx/ebin/jsx_consult.beam: deps jsx ebin jsx_consult.beam
所以MAIN_TO_DEST的结果就是 lib ejabberd-1.0.0 deps jsx ebin jsx_consult.beam, 这是最终的要copy到的target 路径

所以TO_DEST就是最终所有文件的copy目的路径： 比如 /lib/ejabberd-1.0.0/deps/jsx/ebin/jsx_consult.beam /lib/ejabberd-1.0.0/deps/jsx/ebin/xxx.beam /lib/ejabberd-1.0.0/deps/large/ebin/bbb.beam

### make install的执行流程
#### makefile的rule
{% codeblock lang:zsh %}
target... : prerequisites ...
          command
          ...
          ...
{% endcodeblock %}
prerequisites在这个makefile里是指向别的target
command是具体的命令

#### install target介绍
{% codeblock lang:zsh %}
install: all copy-files
	$(shell) zoom/patch.sh
	#
	....
{% endcodeblock %}
install的target需要先执行all和copy-files target，执行完这两个target之后再运行后面的命令
这里注意install有个前提条件是copy-files,这是这次新代码的入口

#### copy-files target介绍
{% codeblock lang:zsh %}
ifeq ($(MAKECMDGOALS),copy-files-sub)
...
...
...
copy-files-sub2: $(call TO_DEST,$(DEPS_FILES) $(MAIN_FILES) priv/bin/captcha.sh priv/sql/lite.sql)
endif

copy-files:
	$(MAKE) copy-files-sub

copy-files-sub: copy-files-sub2
{% endcodeblock %}
copy-files的target执行了命令make copy-files-sub
{% alert info %}
为什么这里单独再使用个make，是因为这段新代码有个开关`ifeq ($(MAKECMDGOALS),copy-files-sub)`,单独用一次make，MAKECMDGOALS是make的一个内置变量，代表make时指定的goal，如果不单独使用make copy-files-sub，这个变量就是install了，单独使用一次make，就能变成copy-files-sub，从而走到新逻辑。
那么为什么不能直接写成`ifeq ($(MAKECMDGOALS),install)`呢？我理解这些变量和语句是在make执行target前就确定好的，比如DEPS，make install也包含了make的过程，编译没结束，二进制文件都没有生成，是无法得到正确的文件列表的，放在这里重新执行一次make，会把所有变量表达式重新执行一遍，并且是在保证make之后
{% endalert %}
copy-files-sub target需要先执行copy-files-sub2
copy-files-sub2就是我们之前分析了一大堆的那句。。。。
copy-files-sub2: $(call TO_DEST,$(DEPS_FILES) $(MAIN_FILES) priv/bin/captcha.sh priv/sql/lite.sql)
这里我开始一直没想明白啥意思，以为copy-files-sub2就是运行后面这句话。绕错了。
再仔细想想make rule的定义，target... : prerequisites ...
$(call TO_DEST,$(DEPS_FILES) $(MAIN_FILES) priv/bin/captcha.sh priv/sql/lite.sql) 是prerequisites
也就是说，需要把这句的返回结果当做prerequisites。那么运算完的结果就应该是类似下面这样的
copy-files-sub2： /lib/ejabberd-1.0.0/deps/jsx/ebin/jsx_consult.beam /lib/ejabberd-1.0.0/deps/jsx/ebin/xxx.beam /lib/ejabberd-1.0.0/deps/large/ebin/bbb.beam
一堆以文件路径命名的targets。

那么这些targets在哪里定义的呢？

#### 目标文件路径定义的targets哪里来的？
其实也是通过template
{% codeblock lang:shell %}
define COPY_template
$(call TO_DEST,$(1)): $(1) $(call TO_DEST,$(dir $(1))) ; $$(INSTALL) -m 644 $(1) $(call TO_DEST,$(1))
endef

$(foreach file,$(DEPS_FILES_FILTERED) $(MAIN_FILES),$(eval $(call COPY_template,$(file))))

$(sort $(call TO_DEST,$(MAIN_DIRS) $(DEPS_DIRS))):
	$(INSTALL) -d $@

$(call TO_DEST,deps/p1_pam/priv/bin/epam): $(LIBDIR)/%: deps/p1_pam/priv/bin/epam $(call TO_DEST,deps/p1_pam/priv/bin/)
	$(INSTALL) -m 750 $(O_USER) $< $@

$(call TO_DEST,priv/sql/lite.sql): sql/lite.sql $(call TO_DEST,priv/sql)
	$(INSTALL) -m 644 $< $@

$(call TO_DEST,priv/bin/captcha.sh): tools/captcha.sh $(call TO_DEST,priv/bin)
	$(INSTALL) -m 750 $(O_USER) $< $@
{% endcodeblock %}
首先看看template的使用
$(foreach file,$(DEPS_FILES_FILTERED) $(MAIN_FILES),$(eval $(call COPY_template,$(file))))
对每个文件都执行了$(eval $(call COPY_template,$(file))))，假设有这个文件deps/jsx/ebin/jsx_consult.beam

根据template计算的结果就是
/lib/ejabberd-1.0.0/deps/jsx/ebin/jsx_consult.beam: deps/jsx/ebin/jsx_consult.beam /lib/ejabberd-1.0.0/deps/jsx/ebin/
	$(INSTALL) -m 644 deps/jsx/ebin/jsx_consult.beam /lib/ejabberd-1.0.0/deps/jsx/ebin/

注意：
- 这时target后面的是所需的文件，不是另外两个target了
- 如果你要使用真实的“$”字符，那么你需要用“$$”来表示，所template里INSTALL是用的$$(INSTALL)
