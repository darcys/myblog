title: =翻译= 2015年devops一览-management层的竞争
date: 2015-09-12 17:16:19
categories:
 - 翻译
tags:
 - devops
 
---

> If you don't like something, change it. If you can't change it, change your attitude.
>如果你不喜欢某事，那就去改变它。如果你不能改变它，那就改变你自己的态度。
>> 玛雅·安吉罗

### 文章简介
the new stack 上一篇非常赞的文章，是对当先management layer的一览
http://thenewstack.io/devops-landscape-2015-the-race-to-the-management-layer/

虽然软文嫌疑比较大，不过写的还是非常全面易懂的，看完之后觉得很清晰，所以翻译一方面是自己加深理解，另一方面也希望对大家有点帮助
由于自己翻译水平实在是很差，还挺怕误导大家，发现不对的还请仔细看原文。


![](http://thenewstack.io/wp-content/uploads/2015/06/13909475735_783a17c607_k.jpg)

> Kevin Fishner is director of customer success at HashiCorp, a leader in the DevOps marketplace. Author note: As an actor in the DevOps ecosystem, we certainly have biases. 
> This summary is our view of the current DevOps environment backed by our experiences and the experiences of our customers. It is not exhaustive.

作者简介，作为devops生态圈的从业者，我们肯定会有些曲解，这个总结是基于我们以及客户的经验，对当前devops生态圈的一些看法，并不彻底。

> There has been so much activity in DevOps tooling and best practices in the past few months that it has become difficult to understand the current and future state of the industry. This summary is the outcome of explaining HashiCorp’s thoughts on the current DevOps landscape and future roadmap.

过去几个月有太多的活动是关于DevOps的tools和最佳实践，以至于我们很难理解当前和未来发展的状态，这个总结是HashiCorp公司对于devops概览和未来发展呢的一些解释

>The Goal of DevOps is to Turn Application Code Into a Running Application Through a Repeatable, Safe Process

## DevOps的目标是让application的code通过一个可重复安全的流程deploy到产品线变成运行的程序

>“DevOps” as a term has many meanings, but for the purposes of this summary “DevOps” will be defined by its goal. Across organizations, the goal of DevOps is to turn application code into a running application through a repeatable, safe process.

DevOps是个有很多解释的术语，但是对于这篇总结，devops是通过它的目标来定义的。DevOps的目标是让application的code通过一个可重复安全的流程deploy到产品线变成运行的程序。

![](http://7xk2xu.com1.z0.glb.clouddn.com/20150912image00-1-1024x250.png)

> The Road to a Complete DevOps Management Layer: Configuration Management vs Immutable Infrastructure

## 通往完整的的DevOps Management层之路：配置管理 vs 不可变基础设施 

> Darcy: 感觉这翻译真不好整啊，比写代码难，这句英文那么通顺，换成中文就不知道该咋说了 #_#

> There are two paradigms to provide a complete management layer for delivering applications

为了递交程序，有两种模式可以实现全面的管理层

> ## Paradigm 1: Runtime Configuration.
> In this view, the workflow from application code to running application is provision a server; configure the server with dependencies and application code; and monitor application and server health with scheduled, continuous configuration management runs.

## 模式1： 实时的配置 
在这个视图， 从代码到运行的程序的工作流是:
* provision一台server
* 在这台server上安装进行系统配置，安装依赖和应用程序
* 安装monitoring和一些cron job

> ## Paradigm 2: Immutable Infrastructure.
> In this view, the workflow from application code to running application code is to package the application into a deployable artifact, such as a Docker container, AMI, jar, etc; then provision the server in the likeness of that artifact; and finally, monitor application and server health. It is very important to note that immutable infrastructure and configuration management are not mutually exclusive. Configuration management tools are often used to configure build-time artifacts.

## 模式2：不可变基础设施
在这个视图里，从代码到运行的程序的工作流是将应用程序打包到一个可以直接部署的artifact中，比如Docker， Ami，jar等等,然后用这个artifact provision一台server，最终监控应用程序和健康状况
需要注意的是，配置管理不可变基础设施和不是互斥的，配置管理工具通常用来配置build阶段的artifacts

> The key difference between immutable infrastructure and configuration management paradigms is at what point configuration occurs. In the immutable infrastructure view configuration happens at build-time, before deployment. In the configuration management view configuration happens at runtime, after deployment. To make a service change in the immutable infrastructure world, a new artifact is built, the old service is destroyed, and a new service is brought up using the new artifact. The service is never changed once it has been deployed, and thus why this paradigm is called “immutable” infrastructure. To make a service change in the configuration management world, the same server is updated with new service code. Again, these paradigms are not mutually exclusive, as configuration management tools are often used to help build build-time artifacts.

这两个模式最关键的区别在于配置何时发生：
* 对于不可变基础设施，config是在build时做的，在deployment之前
* 对于配置管理，配置是运行时做的，在deployment之后

对于这两个模式，当要对server进行改动时的区别：
* 对于不可变基础设施，由于配置是发生在build time，那么理所当然，配置更改时，需要重新build artifact，因此老的server需要destroy掉，然后用新的artifact创建新的server,server在deploy后永远不会改变，所以这个方式被称作“不可变” 
* 对于配置管理，相同的server被新的配置管理的code更新。

再次重申，这两个不是互斥的，配置管理工具经常被用来构建artifacts

> There’s arguably a nascent third paradigm, which treats servers as a generic resource pool which heterogeneous workloads are scheduled on top of. This technically fits under the immutable infrastructure paradigm, but is unique enough to define independently. This paradigm is in active development as Mesos, Kubernetes, Docker Swarm, and various other schedulers gain in popularity.

还有个可论证的刚出现的第三种模式，这个模式把server当做一个通用的资源池，这个池子顶层安排有各式各样的工作，这项技术适合不可变基础设施模式，但是足够特殊到可以单独定义。这个模式是正在活跃的开发中，比如Mesos，Kubernetes和Docker Swarm和多种其他产品等

> ## The Management Layer is the Revenue Opportunity in DevOps
> There are three basic layers in DevOps — infrastructure, tooling, and management:

## 管理层是DevOps是盈利点
基本上DevOps有三层： infrastructure， tooling 和 management
![](http://7xk2xu.com1.z0.glb.clouddn.com/20150912image01.png)

* > ### The infrastructure layer
> is comprised of technology that provides virtualization functionality to physical hardware to make compute, network and storage easily accessible. Example technologies are Amazon’s EC2 and Google Compute Engine in the public cloud arena, along with VMware and OpenStack in the private cloud arena.

infrastructure层主要提供虚拟化的基础设施，比如在公有云方面，有亚马逊aws，谷歌的GAE。在私有云方面，有openstack和vmware

* > ### The tooling layer 
> is comprised of technologies that make access and configuration of the infrastructure layer an easier process. These tools still require functional knowledge of the underlying infrastructure. Examples here are the open source tools by HashiCorp, Mesos, Docker, Puppet, Chef, Jenkins and many, many more. It’s interesting that the majority of the tooling layer is comprised of open source software.

tooling层是为了简化接入管理和基础设施层的流程，这些工具仍然需要基本的基础设施知识，这里有些例子，比如HashiCorp, Mesos, Docker, Puppet, Chef, Jenkins和其他许多，有趣的是，这一层基本都是开源的。

* > ### The management layer 
> is at the top and has the goal of abstracting away both the tooling and infrastructure layers into one common workflow for turning application code into a running application. An example end goal would be to just package your application — potentially as a Docker container, rkt container, jar or binary — and have it be able to run on any cloud or physical infrastructure. Ideally, no knowledge of the infrastructure itself is necessary and all the logic is held at the application level.

管理层是最上层，目标是将tooling and infrastructure layers 合并为一个通用的工作流,从而让产品代码可以转化成上线，举例来说，最终目标可能仅仅需要打包你的应用程序，比如docker容器，rkt容器，jar或者二进制文件，然后让它可以运行在各种cloud或者物理机上。理想状况下，不需要任何infrastructure相关的只是，只需要集中于应用层开发即可。

>The infrastructure layer is facing a wave of competition and commoditization, and we now see the companies in this space trying to move up the stack into tooling and management. Amazon Web Services (AWS) has released tools like EC2 Container Service (ECS) and AWS CodeDeploy, which allow users to simply package their applications and ignore the infrastructure configuration and provisioning processes. Google Cloud Platform has similarly released Google App Engine and Google Container Service, as well as sponsoring open source projects such as Kubernetes. VMware has moved into tooling and management with heavy support for Pivotal’s Cloud Foundry. These tools make it easier for users to deploy applications and allow infrastructure providers to lock users into their platforms.

基础设施层经过一番竞争，地位已经确立，现在我们可以看到这些公司正在往tooling和management发展，aws最近发布了tool，比如EC2 Container Service (ECS)和AWS CodeDeploy，这些工具可以让用户简单的打包自己的应用程序，并且忽略基础设施配置和provision的过程。Google的云平台也有类似的GAE和Google Container Service，并且google也赞助了Kubernetes，Vmvare也做类似的事情。这些工具让用户能轻松的deploy应用程序，并且让基础设施提供商锁定住用户在自己的平台上。

>The tooling layer is more difficult to monetize as the tools themselves solve pieces of the greater DevOps goal. It’s difficult to charge at enterprise levels for tools that package applications or provision infrastructure. Enterprises pay for tools that reduce costs through employee timesavings or material savings. These tools still require significant employee effort and are difficult to tie to material savings, thus are harder to monetize. It is certainly possible to monetize this layer through support and services; however, those are more difficult to scale.

Tooling层比较难作为商品区卖，针对devops庞大的目标，工具只是解决一部分的问题。很难从企业级层面解决整个部署过程，企业购买工具是为了减少成本，包括员工的时间成本和材料的成本，这些tool还是需要工程师很多的工作量，并且也很难节约成本，因此很难商品化，这个层次上赚钱，主要是通过support和提供服务。不过这是很难让利润扩大化的。

>The management layer presents the most significant monetization opportunity as it presents a full solution for turning application code into a running application. The employee investment is significant in the implementation phase, but relatively minimal for upkeep. If the management level can get into material cost savings, such as reducing the size of server fleets, there will be tremendous opportunities for revenue.

管理层有巨大的盈利机会，因为它是一个完整的解决方案， 从而对于员工的投资更多的可以用于实现产品，更少的用于维护产品。如果管理层能节约成本，比如减少服务器的数量，那将是巨大的收入增长点。

>DevOps companies are racing to the management layer; however, several tools are necessary to have a complete DevOps management offering.

DevOps的公司在管理层比赛，然后，很多工具对于完整的DevOps管理层还是必不可少的

> ## Three Types of DevOps Companies: Infrastructure Providers, Configuration Management Companies, and Immutable Infrastructure Companies
>Given that the management layer has the greatest revenue potential, the active players in the space are all battling to capture the opportunity. The interesting aspect of this battle is that the different types of companies in DevOps are approaching it from unique vantage points. These organizations can be categorized as either infrastructure providers, configuration management companies, or companies that favor immutable infrastructure. Amazon Web Services, Google Cloud Platform, OpenStack and VMware are the major players in the infrastructure provider space. Ansible, Chef, Puppet and Salt are the leaders in configuration management. HashiCorp, CoreOS, Docker and Mesosphere are the leaders in the nascent immutable infrastructure space.

## 三种DevOps的公司： 基础设施提供商， 配置管理公司和不变的基础设施公司
因为管理层有极大的盈利点，所以竞争激烈。有趣的是，不同类型的公司通过自己独有的优势来参与这场竞争，这些公司可以被分类为基础设施提供商，配置管理公司和提供不可变基础设施的。在基础设施提供商这层，主要有亚马逊AWS，Google Cloud平台，OpenStack和VMvare。配置管理方面，Ansible，Chef, Puppet和Salt是主要的领导者。在不可变基础设施方面，有HashiCorp, CoreOS, Docker和Mesosphere。

>All of these companies must have solutions for infrastructure provisioning and application packaging or configuration in order to have a viable chance in winning the battle at the management layer. Historically, VMware is a great example of owning the entire tooling stack to win the datacenter virtualization market. If you look at the current product development across the companies, you can see that each is building tooling around these essentials. Chef has Chef Provisioning to provision infrastructure, and Chef configuration management to configure the application. HashiCorp has Terraform to provision infrastructure and Packer to package applications and build machine images. Docker has Docker Machine to provision infrastructure and Docker to package applications.

所有这些公司必须具备提供基础设施，程序打包或者配置管理才能在管理层有机会赢得这场战争，Vmare是个很好的例子，因为它具备具备完整的工具栈，从而赢得了数据中心虚拟化的市场。 如果看一看当前的这些公司产品的开发，你会发现开发tooling都是围绕这些要素. Chef有Chef Provision用来provision基础设施，chef configuration management来配置应用程序。HashiCorp有Terraform来提供基础设施，packer来打包程序，做成镜像，Docker有Docker Machine来provision基础设施，Dock来打包程序

>These tools are then combined into a dashboard or common workflow to provide a management layer. For example, Mesosphere bundles Mesos, Marathon and Chronos to create the Mesosphere Datacenter Operating System. HashiCorp unites Packer, Terraform and Consul to create Atlas. CoreOS recently released Tectonic to bundle etc, rkt and flannel into a management layer. The infographic below shows the tooling portfolios for the major players in these categories and how those tools are starting to develop into a management layer.

这些tool会合并成一个dashboard或者通用的工作流，从而提供管理层。距离，Mesosphere整合了Mesos, Marathon and Chronos来创建Mesosphere数据中心操作系统。HashiCorp整合了Packer，Terraform和Consul来创建Atlas。 CoreOS最近发布了Tectonic来整合rkt和flannel到管理层。下面这幅图描绘了主要的竞争对手在不同类别中的各种tool，并且这些工具是怎么开始进入管理层的
![](http://7xk2xu.com1.z0.glb.clouddn.com/20150912image02.png)

> ## Predictions for the Next 12 Months

## 未来12个月的预测
>This summary identified infrastructure provisioning and application packaging or configuration as essential tools to build the management layer for DevOps. This conclusion leads to a few predictions:

通过这篇总结，得知为了建立一套DevOps的管理层，必须基础设施提供，应用程序打包或者配置的工具。以上结论引出下面的预测

>All companies in the immutable infrastructure space will have tools to provision infrastructure, package applications, schedule applications and monitor applications.

不可变基础设施的公司将会有provision基础设施，打包应用程序，安排应用程序和监控应用程序

>Configuration management companies will get the majority of enterprise budgets. It will take the immutable infrastructure companies more than 12 months to make significant progress with enterprises.

配置管理公司将会主要面向企业，它将比不可变基础设施公司在企业客户上取得巨大的进步。

>AWS will grow by 50 percent plus and soak up a larger percentage of overall infrastructure budgets. The AWS tooling ecosystem is significantly more mature than competitors and more companies are moving away from on-premises datacenters.

AWS将会增长50%并且会占据更大的整个基础设施的市场份额。AWS的tooling生态系统明显比其他对手成熟太多，并且更多的公司放弃on-premises数据中心。

> ## How to Choose a DevOps Management Layer
>The purpose of this summary is to shed light on the crowded and opaque DevOps landscape. However, the obvious next question is, “How do I choose a DevOps management layer?” This question has many moving parts, and the best we can do is try to simplify some of the choices by answering a few questions:

## 如何选择DevOps管理层
这篇总结的目的是阐明拥挤和晦涩的DevOps生态。然后，明显的下一个问题是：“如何选择DevOps管理层呢”

> ### Are two or more individuals working on your team’s infrastructure (part-time contributors are counted)?
> * If no, the two best options are either Amazon Web Services or Heroku. If no one has ops experience, Heroku is the best choice. If someone does have ops experience, AWS is the most full-featured option.
> * If yes, your team needs a way to manage the complexity of collaborating on infrastructure. Move on to the next question.

### 是否有两个或更多的工程师在你team里工作于基础设施（part-time的也算）？
* 如果否，最佳的两个选择是AWS或者Heroku。 如果没人有经验，Heroku是最佳选择。如果有人有ops的经验，AWS是最全面的选择。
* 如果是，你的团队需要一方方法来管理复杂的基础设施方面的协作，继续下一个问题

> ### Do you want to do build-time or runtime configuration?
> * If you want to do runtime configuration, use a configuration management tool such as Ansible, Puppet, Chef or Salt. If you want agent-based configuration management, use Puppet or Chef. If you want ssh-based configuration management, use Ansible or Salt.
> * If you want to do build-time configuration, which means building images and immutable infrastructure, use Atlas by HashiCorp, Mesosphere, Tectonic by CoreOS, or Docker Hub. Move on to the next question to select between these options.

### 你是像用build-time还是实时的配置管理
* 如果你想用实时的配置管理，用Ansible, Puppet, Chef或者Salt等配置管理工具。如果想用基于agent的方式，用Puppet或者Chef。如果想用基于ssh的，用Ansible或者Salt.
* 如果你想用build-time的配置管理，创建镜像并且是不可变基础设施，用hashicorp的Atlas，Mesosphere, Tectonic by CoreOS, or Docker Hub. 对于这些选择，继续往一个问题。

> ### Do you want to automate the infrastructure provisioning process?
> * If yes, Atlas by HashiCorp is the only option. Atlas uses Terraform to layout infrastructure as code and automate the provisioning process. Docker does have Docker Machine to provision Docker hosts; however, it is currently a command-line tool that only manages hosts, not a complete infrastructure.
> * If no, any of the options by HashiCorp, Mesosphere, Docker, and CoreOS can be considered. However it is difficult to deploy applications without a means to automate and reliably provision infrastructure.

### 是否想自动化构建基础设施的流程
* 如果是，（这条不想翻译了，太软文了）
* 如果不是，任何一项都可以。然后也是软文




