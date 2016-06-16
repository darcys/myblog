title: vim养成计划
date: 2015-07-02 20:54:08
categories:
 - Tech for Fun
tags:
 - vim
---

>There will be many temptations in this new world. But as long as you remain brave, truthful and unselfish, you will not fail.
>>新的世界里会有许多诱惑，只要你一直勇敢、诚实、无私，你就不会失败。
>
>——《童话镇》

#### 开篇
如果参加过各种编程比赛的同学，肯定对全键盘编程这个限定条件会有记忆。这个条件是不允许在编程时使用键盘以外的输入设备，比如触摸板和鼠标都是禁止的。
这个时候熟练vim的同学就会如鱼得水了，那大家肯定会反问，熟悉vim就是为了这个？当然不仅仅是！但是我们可以反过来想，比如code retreat这样影响比较广泛的编程活动，为什么会有这样的限定条件呢？肯定不是闲着蛋疼,那么就自己而言，我可以大概说说玩vim的好处。
>有兴趣的可以看看coderetreat关于no mouse的限定条件。
>http://coderetreat.org/facilitating/activity-catalog

- Geek精神，emacs和vim不熟练一个都说不过去。
- 本身自己是个Devops工程师，每天ssh在各种机器上干活，以前有一阵不用vim，但是用一个自我感觉良好的方法，所有的文本编辑在本地各种狂帅霸裤的现代编辑器上搞定，然后到远程机，vi ->  9999999dd -> Cmd +V -> ZZ 
- 效率高，虽然学习曲线陡峭，但是越熟练越快，有些地方是现代编辑器无法比拟的，比如现代编辑器都是通过鼠标定位，鼠标键盘来回切换来工作，而vi一方面会省略掉这个RTT，另一方面一眼就能定位的一些位置用5k，6j, 3w, fk之类的跳转快捷很多。


#### 缘由
完事皆有因，其实自己下决心用好vim还是因为一些触动，如前所述，2，3年以前，我很喜欢自己那套9999dd+ctrl v，那么运维的工作经常会share屏幕大家一起工作，我当时对自己这套做法还觉得不错。并且我并不是不会用，而是懒得用罢了。  
有一日，和一个同事远程协作产品线上trouble shooting，看他用emacs无比娴熟，效率其高，真是无比羡慕，并且同时觉得自己很挫。
后来还下定决心也要把emaces练到他的程度，无奈试了一阵，觉得难度太大，放弃了，好在vi多少有些底子，起码正常编辑没问题，就专心把vim用好吧

#### 养成
如果让我推荐怎么练好vim，我觉得简单可以概括成一句话，随处都用vim。  
- 比如我现在写这篇博客，虽然是在Idea编辑器里，但是用了vim的plugin，实际效果和vim没啥区别。  
- 我也是evernote用户，但是不在客户端里写note，有个替代方案叫geeknote，然后打开终端就可以用vi写evernote，有兴趣可以试试： http://www.geeknote.me/
- 写邮件可以用Vmail或者mutt，https://linuxtoy.org/archives/vmail.html
- 其他文件，如果需要云存储的，可以直接到网盘目录去vi

#### Tips
- 首先丢掉上下左右键，移动词别忘了用w, b
- 用Ctrl+c 替换Esc
- 复杂选文件用:Sex

#### 资源
下面这个链接可真是够宅的。。。
http://blog.jobbole.com/tag/vim/

vim的各种cheatsheet
http://pan.baidu.com/s/1sjE5bjR






