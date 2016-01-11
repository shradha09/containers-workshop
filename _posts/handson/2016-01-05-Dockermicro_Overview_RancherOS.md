---
layout: article
title: "Micro OSes : Rancher OS"
categories: articles
modified: 
tags: []
comments: true
ads: false
---

{% include toc.html %}

#What is RancherOS?

Rancher OS is another of the new generation of slim and trim micro oses geared towards running containers and Rancher takes this literally, being barely 20 mb in size. 

It eliminates everything that is not required to support containers (read docker). In fact, except for the kernel, everything else runs as containers.

![Rancher Architecture](https://github.com/rancher/os/raw/master/docs/rancheros.png "Racher Architecture") 

Yes, PID 1 here is a modified docker process, called the system docker.

Yes, RancherOS creates a special system service container called User Docker, that is created by System Docker. A separate Docker daemon runs in the User Docker container. Because all user containers run inside the User Docker container, deleting all user containers, for example, will not bring down the system containers running RancherOS services.

If you are interested in learning more about rancher, check out their [website](http://rancher.com/rancher-os/). 

#Rancher Lab

##Setting up the lab

The first step in setting up the lab in vagrant is to get the files required for the same available at [github](https://github.com/rancher/os-vagrant). 

You can create a project folder and either download the files or git clone it depending on your preference and convenience. Once done, its a matter of running `$ vagrant up` to get the rancheros going.

Once done, `$ vagrant ssh` into it and let us begin.

##First steps

First off, let us focus on the differences rancher has with respect to other micro oses. For this, let is first look at the processes running on rancher.

###Command : 

    $ ps aux

###Output : 

    PID   USER     COMMAND
        1 root     docker daemon --log-opt max-size=25m --log-opt max-file=2 -s overlay -b docker-sys --fixed-cidr 172.18.42.1/16 --resta
        2 root     [kthreadd]
        3 root     [ksoftirqd/0]
        5 root     [kworker/0:0H]
        7 root     [rcu_sched]
        8 root     [rcu_bh]
       ...
      911 root     sshd: rancher [priv]
      913 rancher  sshd: rancher@pts/0
      914 rancher  -bash
      971 root     [kworker/0:0]
     1079 root     [kworker/u2:0]
     1121 root     /bin/sh
     1139 rancher  ps aux

As can be seen, PID 1 is docker.

Another difference lies in the fact that apart from the linux kernel itself, everything else runs as containers which can be accessed and controlled through `$ system-docker`. This is very similar to the standard docker and hence those parameters will work here.

### Command : 

    $ system-docker ps

###Output : 

    CONTAINER ID        IMAGE                       COMMAND                  CREATED             STATUS              PORTS               NAMES
    f9275bee0ee8        rancher/os-docker:v0.4.1    "/usr/sbin/entry.sh /"   43 minutes ago      Up 43 minutes                           docker
    9a4a89e631eb        rancher/os-console:v0.4.1   "/usr/sbin/entry.sh /"   43 minutes ago      Up 43 minutes                           os_console_1
    801aa477466b        rancher/os-ntp:v0.4.1       "/usr/sbin/entry.sh /"   43 minutes ago      Up 43 minutes                           ntp
    0764aa2a87bd        rancher/os-network:v0.4.1   "/usr/sbin/entry.sh n"   43 minutes ago      Up 44 minutes                           network
    a252def55432        rancher/os-udev:v0.4.1      "/usr/sbin/entry.sh /"   43 minutes ago      Up 43 minutes                           udev
    acea8aefd534        rancher/os-syslog:v0.4.1    "/usr/sbin/entry.sh /"   43 minutes ago      Up 43 minutes                           syslog
    82f33a4cbfbe        rancher/os-acpid:v0.4.1     "/usr/sbin/entry.sh /"   43 minutes ago      Up 43 minutes                           acpid

