title: 重拾hexo,git blog走起
date: 2016-06-02 15:11:48
categories:
 - 个人
tags:
 - blog
---

### 好久没有更新了
上次更新还是去年,有些东西真是一放就是很那拿起来,沉下心花了1个多小时把npm, hexo还有一堆插件都升了升级,然后fix了一些问题,又开始了.
过了一年变化不少:
- 公司的一个项目上线,那阵压力特别大,不过现在系统很平稳了,学习了很多,也收货了很多,对aws的devops工作比较深入.
- 新的项目开始玩serverless,这个开源项目关注aws的应该都知道, aws的microservice解决方案, 话说microservice这个词,去年自从有本书出来后,到今年基本就烂大街了,到处都在谈这个.
- 去年压力最大的时候每天还是要保持运动量,9月份无意接触了街舞, 报名学习了lockin和hiphop,跟了两学期的课,认识了很多朋友和老师,非常懵逼的上了6,7节大师课,也算人生的一片新天地,虽然我这个年纪只能是强身健体娱乐为主,但是!跳舞,我们是认真的.
- 目前开始玩docker的pipeline, 公司的主打项目的运维方式想提高自动化能力,于是结合jenkins,ansible,docker等做一套CD的系统,也挺有意思.
其实想分享的很多,但是往往过了这村就没这个店了,最想分享的时候没时间,过了这个时间点,也不一定能写出来什么东西

### 这次升级刨的坑
#### TypeError: Cannot read property 'compile' of undefined on Mac 
我用的皮肤是tranquilpeak, 升级hexo后generate出错,无需降级的解决方案: 

{% codeblock lang:zsh %}
change source/_css/layout into source/_css/layouts and modify related lines in source/_css/tranquilpeak.scss

@import
    'layouts/about',
    'layouts/blog',
    'layouts/bottom-bar',
    'layouts/cover',
    'layouts/footer',
    'layouts/header',
    'layouts/main',
    'layouts/sidebar';
    
{% endcodeblock %}

#### TypeError: this.relative_url is not a function
修改_config.yml 为 relative_link: false