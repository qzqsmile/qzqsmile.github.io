---
title: "K8s每讲笔记"
date: 2023-10-31T22:51:40+08:00
lastmod: 2023-09-27T22:51:40+08:00
draft: false
keywords: []
description: ""
tags: []
categories: []
author: ""

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: false
toc: false
autoCollapseToc: false
postMetaInFooter: false
hiddenFromHomePage: false
# You can also define another contentCopyright. e.g. contentCopyright: "This is another copyright."
contentCopyright: false
reward: false
mathjax: false
mathjaxEnableSingleDollar: false
mathjaxEnableAutoNumber: false

# You unlisted posts you might want not want the header or footer to show
hideHeaderAndFooter: false

# You can enable or disable out-of-date content warning for individual post.
# Comment this out to use the global config.
#enableOutdatedInfoWarning: false

flowchartDiagrams:
  enable: false
  options: ""

sequenceDiagrams: 
  enable: false
  options: ""

---

<!--more-->
主要记录一下即可时间中每一讲的知识要点。

容器的编排 10.为什么需要Pod 因为很多功能是由一些进程组来实现的，而容器又是一种单进程模型，那么就面临着一个问题，如何把这些进程调度到一台机器上。很显然有两种思路,

1. 依然对容器进行调度，在算法层面解决调度到一台机器上的问题
2. 将这些容器看做一个整体
   
K8s就是采取的第二种，即把它看成一个Pod的module 另一个原因则是，由于Pod里的容器共享了某些资源，比如network namespace. 这样可以更好采用sidecar的方式实现容器设计的模式。即在主容器外，设计一些辅助容器实现一些辅助的功能。 这里提一句，现在大火的Isito其实就是利用这种设计模式实现的。

18-20. k8s stateful的部署 这里主要利用stateful的部署的原因是，很多引用都是有状态，不能简单的scale out和restart,因为这样做的话状态会丢失。 这里很直观的想法就是把这些状态持久化存起来，当Pod重启的时候重新加载这些持久化信息。事实上K8s也是这么做的，对于Pod，其需要持久的话信息主要是两个方面。

1. Ip信息，简单来说就是保证重启后仍能寻址到相应Pod。
2. 应用运行时产生的一些状态信息。 对于这两种信息的持久化都要面临一种问题就是，怎么使的新launch的pod和之前的pod对应起来。K8s的stateful采取的是名字唯一化这种方法，对于一个stateful的pods，其名字是始终唯一的(会加入编号)。即使重启也是这样。 这样使的同一个类型的应用重新launch后，也能和之前持久化的一些信息对应起来。 对于网络信息的状态的保存，利用DNS机制，因为pod name一样，所以即使重启也很快可以寻址到新的Pod 应用的状态信息则是通过PVC和PV的方式绑定到持久化存储上。
