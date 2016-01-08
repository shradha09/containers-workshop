---
layout: article
title: "Micro OSes : Project Atomic"
categories: articles
modified: 
tags: []
comments: true
ads: false
---
{% include toc.html %}
 
#Atomic Lab

Al right, its time to try out a bit of Project Atomic

##Setting Up

The first step for setting up the lab is to get the [Atomic Image](https://getfedora.org/cloud/download/atomic.html). 

You can try out the other images later, but for now get the virtual box image, which will give you an atomic fedora box. Once the download is complete, at the box with the command

    $ vagrant add Atomic/FedoraAtomic file://path/to/fedora/box

Once this is done, choose a folder to act as your project folder, make sure it is your present working directory and initialise and start an atomic container.

    $ vagrant init Atomic/FedoraAtomic
    $ vagrant up

Now ssh into your machine using your preferred method, though you can just use 

    $ vagrant ssh

If everything worked out, you should be in the atomic host, logged in as vagrant and you can, as needed switch to root with 

    $ sudo -i

Remember that is lab is meant to get you started only and you can continue your study on the Project Atomic website, should you be interested. 

Now that we are setup, let us begin. 

##First Steps

To get you started run, the following on the atomic host that you ssh'd into. 

###Command :

    $ atomic --help

###Output :

    usage: atomic [-h] [-v]
                  {host,info,install,images,mount,push,upload,scan,stop,run,uninstall,unmount,update,version,verify}
                  ...
    
    Atomic Management Tool
    
    positional arguments:
      {host,info,install,images,mount,push,upload,scan,stop,run,uninstall,unmount,update,version,verify}
                            commands
        host                execute Atomic host commands
        info                display label information about an image
        install             execute container image install method
        images              list container images on your system
        mount               mount container image to a specified directory
        push (upload)       push latest image to repository
        scan                scan an image or container for CVEs
        stop                execute container image stop method
        run                 execute container image run method
        uninstall           execute container image uninstall method
        unmount             unmount container image
        update              pull latest container image from repository
        version             display image 'Name Version Release' label
        verify              verify image is fully updated
    
    optional arguments:
      -h, --help            show this help message and exit
      -v, --version         show program's version number and exit

  As can be seen, there are multiple options available
  
##Status information of Atomic Host

Now lets try to get some information about the atomic host itself. To do so, run

###Command :

    $ atomic host status

###Output :

      TIMESTAMP (UTC)         VERSION   ID             OSNAME            REFSPEC
    * 2015-12-14 10:16:00     23.33     a40cbd4a80     fedora-atomic     fedora-atomic:fedora-atomic/f23/x86_64/docker-host

##Updating the Atomic Host
One of the primary strengths of Atomic Hosts is the ability to do 'Image based Updates'. In fact, that’s where the name Atomic comes from. This also allows for easy roll backs with the help of [rpm-ostree](https://wiki.gnome.org/Projects/OSTree).
To update the atomic host itself, you can run 

###Command : 

    $ atomic host upgrade

###Output : 

    Updating from: fedora-atomic:fedora-atomic/f23/x86_64/docker-host
    
    790 metadata, 4053 content objects fetched; 168868 KiB transferred in 479 seconds
    Copying /etc changes: 22 modified, 0 removed, 48 added
    Transaction complete; bootconfig swap: yes deployment count change: 1
    Changed:
      atomic 1.6-5.git09ac479.fc23 -> 1.7-2.gitd378de0.fc23
     ...
      xkeyboard-config 2.16-1.fc23 -> 2.16-2.fc23
    Removed:
      rpm-build-libs-4.13.0-0.rc1.7.fc23.x86_64
      rpm-python3-4.13.0-0.rc1.7.fc23.x86_64
    Run "systemctl reboot" to start a reboot
Obviously, a restart will be required to actually apply the updates.

Before the restart, if we check the status of the atomic host, we will get 

     TIMESTAMP (UTC)         VERSION   ID             OSNAME            REFSPEC
      2016-01-05 21:40:09     23.45     98be0ae0f9     fedora-atomic     fedora-atomic:fedora-atomic/f23/x86_64/docker-host
    * 2015-12-14 10:16:00     23.33     a40cbd4a80     fedora-atomic     fedora-atomic:fedora-atomic/f23/x86_64/docker-host

After reeboot, however, you will see that things have changed

     TIMESTAMP (UTC)         VERSION   ID             OSNAME            REFSPEC
    * 2016-01-05 21:40:09     23.45     98be0ae0f9     fedora-atomic     fedora-atomic:fedora-atomic/f23/x86_64/docker-host
      2015-12-14 10:16:00     23.33     a40cbd4a80     fedora-atomic     fedora-atomic:fedora-atomic/f23/x86_64/docker-host

##Creating and running a container

Instead of using docker command directly, which may require us to remember a lot of parameters, we can directly use the below command to deploy containers (this makes use of docker labels to ease the process of setting up images and containers), which will automatically pull the necessary image from configured repositories.

###Command : 

    $ atomic run docker.io/busybox

###Output : 

    Using default tag: latest
    Trying to pull repository docker.io/library/busybox ... latest: Pulling from library/busybox
    c00ef186408b: Pull complete
    ac6a7980c6c2: Pull complete
    Digest: sha256:e4f93f6ed15a0cdd342f5aae387886fba0ab98af0a102da6276eaf24d6e6ade0
    Status: Downloaded newer image for docker.io/busybox:latest
    
    /usr/bin/docker create -t -i --name busybox docker.io/busybox sh
    busybox

##Check existing image labels

Atomic also gives us an easy way to check the labels of any existing image. For that, first let us see what images are already available. 

> Note : I have already pulled a centos image as before which can be done in the same way we pulled the busybox image or you can build your own docker image with labels and then simply replace centos with your image name.

###Command :

    $ atomic images

###Output : 

     REPOSITORY                          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
     docker.io/centos                    latest              c8a648134623        2015-12-24 15:30    196.63 MB
     docker.io/busybox                   latest              ac6a7980c6c2        2015-12-08 18:31    1.11 MB

Al right lets check the labels on the centos image.

###Command : 

    $ atomic info docker.io/centos

###Output : 

    license: GPLv2
    build-date: 2015-12-23
    vendor: CentOS
    name: CentOS Base Image

##Cockpit

Cockpit is a web based tool that allows us to easily manage the containers and images present on the atomic host. To utilise this, you need to add the cockpit container to the atomic host. You can do this with the following command

###Command

    $ atomic run cockpit/ws

###Output

    Using default tag: latest
    Trying to pull repository docker.io/cockpit/ws ... latest: Pulling from cockpit/ws
    
    a83f22c39f43: Pull complete
    ...
    70fa12b92196: Pull complete
    Digest: sha256:77f2e420022ca69851128d421e895fe5bdb000563fb489fc16375efd8b8a1657
    Status: Downloaded newer image for docker.io/cockpit/ws:latest
    
    /usr/bin/docker run -d --privileged --pid=host -v /:/host cockpit/ws /container/atomic-run --local-ssh
    /usr/bin/docker run -d --privileged --pid=host -v /:/host cockpit/ws /container/atomic-run --local-ssh
    c43274e8133d372d4d25c3baec1ff1107c6f8528cd3cd672021e7c267b8e5359

Once, you have this, you can access the cockpit interface with the url

    http://(YourHost):9090

You need to make sure that you can access the atomic host from the outside world as the host itself is devoid of a web browser. 

For those using vagrant and virtualbox, they can open the virtual box > Settings of atomic host > Network > Set it as Bridged

> Note : You will no longer be able to `$ vagrant ssh` once this has been done but you can use normal ssh communication or just access it from the virtualbox like any other VM.

We have a few screen shots that you can see though and they are as below : 

###Cockpit login screen

![Cockpit login screen]({{ site.url }}/images/cockpit/cockpit1.PNG "Cockpit login screen")

----------

###Cockpit first screen

![Cockpit first screen]({{ site.url }}/images/cockpit/cockpit2.PNG "Cockpit first screen")

----------

###Cockpit containers and images

![Cockpit containers and images]({{ site.url }}/images/cockpit/cockpit3.PNG "Cockpit containers and images")

----------

###Cockpit containers and images : Running container from image

![Cockpit containers and images : Running container from image]({{ site.url }}/images/cockpit/cockpit4.PNG "Cockpit containers and images : Running container from image")

----------

###Cockpit containers and images : Container running

![Cockpit containers and images : Container running]({{ site.url }}/images/cockpit/cockpit5.PNG "Cockpit containers and images : Container running")

----------

###Cockpit containers and images : Container details with terminal access

![Cockpit containers and images : Container details with terminal]({{ site.url }}/images/cockpit/cockpit6.PNG "Cockpit containers and images : Container details with terminal")

----------

###Cockpit atomic host with terminal

![Cockpit atomic host with terminal]({{ site.url }}/images/cockpit/cockpit7.PNG "Cockpit atomic host with terminal")

##References for further reading

 - [Project Atomic](http://www.projectatomic.io)
 - [Storage Back ends](http://nkhare.github.io/data_and_network_containers/storage_backends/)
 - [Setting up storage on Atomic](http://www.projectatomic.io/docs/docker-storage-recommendation/)
