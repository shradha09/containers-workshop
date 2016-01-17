---
layout: article
title: "Installing Docker"
categories: articles
modified: 
tags: []
comments: true
ads: false
---

Docker packages are available for most, if not all Linux flavours. You can install it simply by using the respective package managers. Do make sure, however that all prerequisites are met before you attempt an installation.

For windows and mac users, however, its a little bit more complicated than that. Since docker isn’t currently natively supported on these Operating systems, yet, we are going to have to use **virtualization** to achieve it. To make things a little simpler, there is something called as the [docker toolbox](https://www.docker.com/docker-toolbox "Docker toolbox download page.") available.

Do make sure of two things, however, before you begin.

 1. Make sure that virtualization is supported and enabled in your machine (check with your manufacturer on how to do so, if not enabled).
 2. Try to ensure no other virtualization hyper-visors (such as vmware or hyperv) are installed.

The docker toolbox will install a collection of necessary tools such as the docker binaries, docker machine and virtualbox needed to support virtualization. You can refer the docker machine page to understand how it works. 

> **Note :** The docker machine is also available to linux users as well.

For further understanding on docker installation, you may refer the [docker installation documentation](https://docs.docker.com/engine/installation/ "Docker installation documentation page").
