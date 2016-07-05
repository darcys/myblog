title: 从零开始使用Atom
date: 2016-06-22 13:45:03
categories:
 - Tech for Fun
tags:
 - vim
---

<!-- toc -->
### 开始折腾atom
作为git粉,迟早还是要转到atom的,不如趁早(其实现在也不算早了)

## download
没啥好说的,官网下载mac版,解压,拷贝到app下面

## apm
安装atom之后,就会自带个命令行工具apm,这个不能更赞了,很多事情就可以脚本化来做了.
apm - Atom Package Manager powered by https://atom.io



支持的命令列表
{% codeblock lang:shell %}
    clean, config, dedupe, deinstall, delete, dev, develop, disable, docs,
    enable, erase, featured, home, i, init, install, link, linked, links, list,
    ln, lns, login, ls, open, outdated, publish, rebuild, rebuild-module-cache,
    remove, rm, search, show, star, starred, stars, test, uninstall, unlink,
    unpublish, unstar, update, upgrade, view.
{% endcodeblock %}

如果想看某个命令的用法,run apm help command
{% codeblock lang:shell %}
apm help dedupe                                                                                                         master 

Usage: apm dedupe [<package_name>...]

Reduce duplication in the node_modules folder in the current directory.

This command is experimental.

{% endcodeblock %}
<!-- more -->

## 配置theme
我还是喜欢light的主题,挑了个最火的:
https://atom.io/themes/atom-material-ui
两个命令安装light theme
{% codeblock lang:shell %}
apm install atom-material-ui
apm install atom-material-syntax-light
{% endcodeblock %}
然后进preferences里面配置一下

## 配置vim
https://atom.io/packages/vim-mode
和其他ide一样,也有vim的plugin
{% codeblock lang:shell %}
apm install vim-mode
{% endcodeblock %}
需要重启一下生效

## 安装其他需要的plugin
https://atom.io/packages
基本上在这里搜就可以了,非常有玩头

除此之外，也可以直接在atom里面打开install菜单
command+shift+P 然后输入 setting view: install packages and themes

## 常用快捷键 （太熟悉的就不列了）
### 文件操作
cmd-alt-s 保存全部的打开文件
cmd-shift-s 另存为
cmd-shift-O 打开目录文件
cmd-\ 显示/隐藏左侧栏

### 文件搜索
cmd-T 在以打开的文件里搜索文件
cmd-B 在打开的文件里搜索
cmd-shift-B 在最近git 修改的文件里搜索

### tree view
cmd-shift-\ 文件定位到treeview
cmd-\ 打开关闭tree view
ctrl-shift-C copy full path

### 文本搜索
cmd-F 在当前文件里搜索
cmd-G 查找下一个
cmd-shift-f 在整个项目里搜索

### 自动补全
ctrl-space 自动补全

### 分割panel
cmd-k arrow 向箭头方向分割窗口

### 语法高亮选择
ctrl-shift-L 然后输入需要解释成什么样的语言

### 左右移动打开的文件
cmd-alt arrow

## 必须要看的手册
http://flight-manual.atom.io
上面提到的这里都有


## 安装term2
apm install term2
### trouble shooting
如果遇到下面的错误
npm ERR! Failed at the pty.js@0.2.13 install script 'node-gyp rebuild'.
按此方法fix
{% codeblock lang:shell %}
mkdir /tmp/pty.js
git clone https://github.com/chjj/pty.js.git /tmp/pty.js
vi /tmp/pty.js/src/unix/pty.cc
replace line 39: #include <util.h> with #include "/usr/include/util.h"
npm install -g /tmp/pty.js
and now npm install -g devtools-terminal should work.
{% endcodeblock %}
然后再apm安装

### 快捷键
https://atom.io/packages/term2

## file-type plugin
会让treeview根据不同文件类型显示不同图标，非常直观
https://atom.io/packages/file-type-icons

## 一键代码美化
https://atom.io/packages/atom-beautify

## 使用
默认就支持python和yaml的语法高亮,我现在就写写script和ansible,暂时不把他弄太heavy了

## markdown
ctrl-shift-M 预览markdown

markdown插件markdown-writer，安装和使用看以下链接
https://github.com/zhuochun/md-writer

安装完之后setting一下，把blog引擎设置为hexo

功能和快捷键可以打开cmd-shift-p
markdown-writer:open-cheat-sheet

如果有快捷键冲突，可以修改,cmd-shift-p
Markdown Writer: Create Default keymaps
修改后立即生效，非常方便
{% codeblock lang:shell %}
".platform-darwin atom-text-editor:not([mini])":
  "shift-cmd-K": "markdown-writer:insert-link"
  "shift-cmd-I": "markdown-writer:insert-image"
  "cmd-i":       "markdown-writer:toggle-italic-text"
  "cmd-b":       "markdown-writer:toggle-bold-text"
  "cmd-'":       "markdown-writer:toggle-code-text"
  "cmd-k":       "markdown-writer:toggle-keystroke-text"
  "cmd-h":       "markdown-writer:toggle-strikethrough-text"
  'cmd->':       "markdown-writer:toggle-blockquote"
  'cmd-"':       "markdown-writer:toggle-codeblock-text"
  "ctrl-alt-1":  "markdown-writer:toggle-h1"
  "ctrl-alt-2":  "markdown-writer:toggle-h2"
  "ctrl-alt-3":  "markdown-writer:toggle-h3"
  "ctrl-alt-4":  "markdown-writer:toggle-h4"
  "ctrl-alt-5":  "markdown-writer:toggle-h5"
  "shift-cmd-O": "markdown-writer:toggle-ol"
  "shift-cmd-U": "markdown-writer:toggle-ul"
  "cmd-j cmd-p": "markdown-writer:jump-to-previous-heading"
  "cmd-j cmd-n": "markdown-writer:jump-to-next-heading"
  "cmd-j cmd-d": "markdown-writer:jump-to-reference-definition"
  "cmd-j cmd-t": "markdown-writer:jump-to-next-table-cell"
{% endcodeblock %}


## diff
atom默认没有支持diff，不像pycharm之类的可以选两个文件cmd-d之类的，那只有依靠插件了。
https://atom.io/packages/split-diff
试了一下这个插件，基本是打开左右两个panel，然后两个panel各自打开所需的文件，然后按cmd-shift-p,输入split diff: enable即可diff，稍微麻烦些。
