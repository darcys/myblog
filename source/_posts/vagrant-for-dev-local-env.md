title: vagrant-for-dev-local-env
date: 2016-06-27 21:39:28
tags:
---

<!-- toc -->

## 目标
为了部署标准化，需要将本地开发环境，测试环境，生产环境统一。那么部署本地环境也需要自动化，从而保证标准。
考虑跨平台的需求并且开发易于部署，所以选择vagrant作为虚拟机标准化的工具。

## 安装并启动centos7

### 安装virtual box
https://www.virtualbox.org/wiki/Downloads
选择合适自己的平台下载并安装

### 安装 vagrant
https://www.vagrantup.com/downloads.html
下载并安装

<!-- more -->
### 想试试最基本的例子，可以过一遍getting started
https://www.vagrantup.com/docs/getting-started/

### 找到需要的box
http://www.vagrantbox.es
这里可以搜到自己需要的的box

### 将centos7的box下载到本地并启动
因为从repo下比较慢，所以可以用上面的地址找到box的link，通过下载工具下载
下载到本地之后，通过以下步骤launch起来centos7 box

#### 在本地增加devops-centos7的box
vagrant box add devops-centos7 ./CentOS-7.1.1503-x86_64-netboot.box

这是run 'vagrant box list' 可以看到刚添加的box

#### 到准备工作的目录（随便什么目录）后执行
vagrant init devops-centos7

#### 启动box
vagrant up

{% codeblock lang:shell %}
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Importing base box 'devops-centos7'...
==> default: Matching MAC address for NAT networking...
==> default: Setting the name of the VM: workplace_default_1467034274331_92681
==> default: Clearing any previously set forwarded ports...
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
==> default: Forwarding ports...
    default: 22 (guest) => 2222 (host) (adapter 1)
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2222
    default: SSH username: vagrant
    default: SSH auth method: private key
    default:
    default: Vagrant insecure key detected. Vagrant will automatically replace
    default: this with a newly generated keypair for better security.
    default:
    default: Inserting generated public key within guest...
    default: Removing insecure key from the guest if it's present...
    default: Key inserted! Disconnecting and reconnecting using new SSH key...
==> default: Machine booted and ready!
==> default: Checking for guest additions in VM...
==> default: Mounting shared folders...
    default: /vagrant => /Users/darcy/Documents/devops-vagrant/workplace
{% endcodeblock %}

使用命令： vagrant global-status 可以看到当前虚拟机的状态

#### ssh 连接到centos7
在Vagrant file所在的目录执行
vagrant ssh

#### 设置public network，方便访问
修改Vagrantfile，增加public network config
config.vm.network "public_network"
如果预先分配了ip，可以用下面的命令固定ip
config.vm.network "public_network", ip: "10.100.80.223"

vagrant reload 生效

vagrant ssh连接后ip addr看到ip


## ansible的集成
vagrant与ansible的集成参见下面的链接
https://www.vagrantup.com/docs/provisioning/ansible.html

不过看完这个介绍，因为我需要的环境是明确的，我觉得不如直接用script控制ansible的执行，这样开发也能更加理解ansible的执行过程

ssh vagrant@127.0.0.1 -p 2222
password: vagrant
