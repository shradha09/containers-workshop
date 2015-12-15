---
layout: article
title: "Introduction"
categories: articles
modified: 2014-08-27T11:57:41-04:00
tags: [concept]
comments: true
ads: false
---

{% include toc.html %}

I am covering some of the basics here. More details can be found in the free sample of my [Docker Cookbook](https://play.google.com/books/reader?id=CzfdCQAAQBAJ&pg)

## Basics

### Containers Vs VMs

<a href="Containers Vs VMS"><img src="http://www.rightscale.com/blog/sites/default/files/docker-containers-vms.png" align="center" height="400" width="600" ></a>

### Underlying Kernel Features and Execution Driver

<a href="Docker Execution driver"><img src="http://blog.docker.com/wp-content/uploads/2014/03/docker-execdriver-diagram.png" align="center" height="400" width="600" ></a>


<iframe src="//www.slideshare.net/slideshow/embed_code/key/n3LCu0qKC9zMxp?startSlide=10" width="595" height="485" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe> <div style="margin-bottom:5px"> <strong> <a href="//www.slideshare.net/Docker/cgroups-namespaces-and-beyond-what-are-containers-made-from" title="Cgroups, namespaces and beyond: what are containers made from?" target="_blank">Cgroups, namespaces and beyond: what are containers made from?</a> </strong> from <strong><a href="//www.slideshare.net/Docker" target="_blank">Docker, Inc.</a></strong> </div>

#### Namespaces
- `pid`
- `net`
- `ipc`
- `mnt`
- `uts`
- `user`

#### CGroups
For resource limitations and accounting for container SELinux/AppArmor

#### Union File-System
- `AUFS`
- `btrfs`
- `vfs`
- `Device Mapper`

<a href="Docker Union File system"><img src="http://blog.linoxide.com/wp-content/uploads/2015/03/docker-filesystems-busyboxrw.png" align="center" height="400" width="600" ></a>

### Docker Architecture
<a href="Docker Architecture"><img src="https://docs.docker.com/article-img/architecture.svg" align="center" height="400" width="600" ></a>


##  Basic Container Operations
<script type="text/javascript" src="https://asciinema.org/a/27188.js" id="asciicast-27188" async data-theme="solarized-dark"></script>

- Make sure you have setup ready, as mentioned in earlier section   

<pre>
$ vagrant up
$ vagrant ssh labvm-1
$ sudo -s
</pre>


- Start the container

```
docker run -it  --name centos1 centos bash
docker run -it --rm  --name centos2 centos bash
```

- Start the the container in background

```
docker run -d  --name centos2 centos  /bin/bash -c "while [ 1 ] ; do echo LinuxConEU2015 ; sleep 1; done"
```

- Looking at the logs of container

```
docker logs centos2
```

- Listing containers

```
docker ps
docker ps -a
docker ps -a -q
```

- Stopping a container

```
docker stop centos2
```

- Stopping all containers

```
docker stop `docker ps -a -q`
```

- Deleting a container

```
docker rm centos2
```

- Deleting all containers

```
docker rm  -f `docker ps -a -q`
```

- Injecting new process inside a running container

```
docker run -d  --name centos3 centos  /bin/bash -c "while [ 1 ] ; do echo LinuxConEU2015 ; sleep 1; done"
docker exec -it centos3 bash
$ ps aux
```

- Inspecting a container

```
docker inspect centos3
```

##  Basic Image Operation

<script type="text/javascript" src="https://asciinema.org/a/27244.js" id="asciicast-27244" async data-theme="solarized-dark"></script>

- Pulling an image

```
docker images
docker search fedora
docker pull <image>
```

- Creating an image from container

```
docker run -idt  --name busyboxC busybox /bin/sh
docker commit -a "Neependra" -m "busybox updated" busyboxC  linuxcon/mybusybox
```

- Exporting an image

```
docker save -o  mybusybox.tar linuxcon/mybusybox
```

- Deleting an image

```
docker rmi linuxcon/mybusybox
```

- importing an image

```
cat mybusybox.tar | docker import - linuxconeu/mybusybox
```

- Building image from Dockerfile

```
echo i “FROM docker.io/centos” > Dockerfle
echo  “RUN date > ~/date” >> Dockerfile
cat Dockerfile | docker build -t centoswithdate1 -
docker images
```

More details about Dockerfiles can be found on [Docker Website] (https://docs.docker.com/reference/builder/)
