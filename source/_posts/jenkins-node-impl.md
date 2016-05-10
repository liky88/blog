---
title: 如何使用Jenkins实现多平台并行集成
date: 2016-05-10 16:21:26
tags: [Jenkins,Node]
categories: Jenkins
---


# **通过对Node的研究，总结如下**：  

**方法1**：  
1. 添加两个节点（Slave node），分别命名为x86-linux-ci-slave和x86-solaris-ci-slave，并且为这两个节点设置了一个相同的标签"foo-ci-slaves"。  
2. 创建一个新的Job，命名为foo-multiplatform-ci，选择的是"构建一个自由风格的软件项目(Build a free-style software project)"。  
3. 为了使得该Job执行并行集成，我选择了"Restrict where this project can be run"，在"Label Expression"中填上了"foo-ci-slaves"，其他配置这里就不赘述了。  

**Result**: 期望两个Slave Node上就会同时进行相关的集成。但Build Log告诉我事实并非我想象的那样：Jenkins只是在一个Slave Node上执行了Job。  
<!--more-->
**方法2**：  
1. 添加两个节点（Slave node），分别命名为x86-linux-ci-slave和x86-solaris-ci-slave，并且为这两个节点设置了一个相同的标签"foo-ci-slaves"。  
2. 应该选择"构建一个多配置项目(Build multiconfiguration project)"。  
3. multiconfiguration project的配置页面中不见了"Restrict where this project can be run"配置选项，但却多出了一个"Configuration Matrix"配置区域。  

在该区域中，我们可以选择Slaves，在Node/Label中，我们可以看到当前Jenkins中配置的所有Label和Nodes。  
选择一个Label是无法满足我们的要求的，那样Jenkins只会从Label中的若干个节点中选择一个来执行集成。  

所以我选择Nodes，将x86-linux-ci-slave和x86-solaris-ci-slave都选上，保存后我们就会在"foo-multiplatform-ci" Job的主页面上看到两个configuration: x86-linux-ci-slave和x86-solaris-ci-slave  

**Result**: 点击"立即构建"，这两个configuration对应的小球标志就会同时闪动，这说明"foo-multiplatform-ci"正在两个Slave  Node上并行运行，bingo！


**Reference**: <http://tonybai.com/2012/02/15/intergating-on-multiple-platforms-simultaneously-using-jenkins/>


# Free-style Software Project based Node Integration
{% asset_img jenkins-node-imp-p1.png %}
{% asset_img jenkins-node-imp-p2.png %}

# Multiconfiguration Project based Node Integration
{% asset_img jenkins-node-imp-p3.png %}
{% asset_img jenkins-node-imp-p4.png %}
{% asset_img jenkins-node-imp-p5.png %}
{% asset_img jenkins-node-imp-p6.png %}
{% asset_img jenkins-node-imp-p7.png %}


