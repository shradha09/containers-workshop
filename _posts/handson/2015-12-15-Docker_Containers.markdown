---
layout: article
title: "Containers"
categories: articles
modified: 2014-08-27T11:57:41-04:00
tags: [concept]
comments: true
ads: false
---

{% include toc.html %}

# Before we start container Operations..

##Listing/ searching an image

As we know, we require images to start containers. These containers are available in docker registries, from where we can get them. But first, we need to know if the image we need is available in the registry.

So lets try searching an image. For the purpose of this tutorial, we will use a small, minimalistic image, but you are welcome to use any image of your choice.

###Command : 

    $ docker search "IMAGE_NAME"

Example : $ docker search ubuntu

###Output : 


INDEX       NAME                                        DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
docker.io   docker.io/ubuntu                            Ubuntu is a Debian-based Linux operating s...   3079      [OK]       
docker.io   docker.io/ubuntu-upstart                    Upstart is an event-based replacement for ...   61        [OK]       
docker.io   docker.io/torusware/speedus-ubuntu          Always updated official Ubuntu docker imag...   25                   [OK]
docker.io   docker.io/lynxtp/ubuntu                     https://github.com/lynxtp/docker-ubuntu         0                    [OK]
docker.io   docker.io/rallias/ubuntu                    Ubuntu with the needful                         0                    [OK]
docker.io   docker.io/teamrock/ubuntu                   TeamRock's Ubuntu image configured with AW...   0                    [OK]


## Pulling an image

If we need an image, that is not currently available to us, we can simply pull the required image from a docker registry. Lets try to pull the image we just searched out.

###Command : 

    $ docker pull tianon/true

###Output

    Using default tag: latest
    Trying to pull repository docker.io/tianon/true ... latest: Pulling from tianon/true
    
    3bc7a2e07962: Pull complete
    bc84b1f88237: Pull complete
    Digest: sha256:0c678029118314264306b49c931d6dce678c8c88143252342a8614210bea4129
    Status: Downloaded newer image for docker.io/tianon/true:latest

## Listing images

Now before we use any image to create containers, we might want to check which containers are currently available on the system. We can do this too.

###Command : 

    $ docker images

###Output : 

    REPOSITORY              TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
    docker.io/tianon/true   latest              bc84b1f88237        9 hours ago         125 B
    docker.io/busybox       latest              0f864637f229        3 months ago        2.43 MB
    docker.io/mysql         latest              6762f304c834        4 months ago        283.5 MB
    docker.io/centos        latest              0f73ae75014f        4 months ago        172.3 MB

# Container Operations

Now that we know some basics about images that we needed to work with containers, lets start off

## Starting a container

Lets attempt to start a  container. We shall use the busybox image here and try to run the `date` command.

###Command : 

    $ docker run busybox date

###Output : 

    Fri Jan 15 06:55:01 UTC 2016

Now lets attempt to run a more powerful container using the centos image.

###Command : 

    $ docker run centos bash

###Output : 

    You should see any output which should not have been the case considering we ran a bash shell. 

Why is this so, because by default, containers do not run in interactive mode. In fact they execute the command passed and them immediately die. So lets try that again, shall we except this time, lets make it interactive

###Command : 

    $ docker run -it centos bash

###Output : 

    [root@2edfbcdc14a4 /]#

Yes, you have now been logged into the shell of the container. You can verify by running standard centos/linux commands here. Once you are done, you may exit. Do note that when you exit, the container dies.

## Starting in background

Now what if, we need the containers to start and continue to run unless we explicitly kill them. This will usually be the case in production as the container might be used to run some amazing stuff of Apache or it might be the server for the next candy crush saga. We can achieve this by simple attaching the `-d` option to our previous command.

###Command : 

    $ docker run -it -d centos bash

###Output

    329c6d07d0cdebde96b019fc7287334e36e80b76ccb1a78333b1a8b254372ef9

Yes, the output is the ID of the container you just started (it will vary for you). The container we started will continue to run unless we explicitly stop or kill it.

## Listing

Al right, now let us take a look at the container(s) we have started.

###Command :

    $ docker ps

###Output : 

    CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
    329c6d07d0cd        centos              "bash"              3 minutes ago       Up 3 minutes                            tender_shockley

Notice that we started a lot more containers till now, but only the ones that are up will be shown by this command. If you want to see ALL the containers you started, then simple attach the `-a` option to the above command.

###Command : 

    $ docker ps -a

###Output : 

    CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                       PORTS               NAMES
    329c6d07d0cd        centos              "bash"              6 minutes ago       Up 6 minutes                                     tender_shockley
    2edfbcdc14a4        centos              "bash"              18 minutes ago      Exited (127) 6 minutes ago                       evil_kirch
    d4c9adbe5e79        centos              "bash"              21 minutes ago      Exited (0) 21 minutes ago                        grave_fermat
    2530e527039f        busybox             "date"              23 minutes ago      Exited (0) 23 minutes ago                        tender_sammet
    da432b604a3d        tianon/true         "bash"              23 minutes ago      Created                                          fervent_bose
    79deb18aff1d        busybox             "bash"              24 minutes ago      Created                                          determined_saha

## Looking at logs

We can look at the log files of existing containers. To do so, let us first start a container running in an infinite loop.

###Command :

    $ docker run -d centos /bin/bash -c "while [ 1 ]; do echo hello docker; sleep 1; done"

###Output : 
    91f4dc4b32b92e05aaf1ea90ec0a02a7d03d7c00248a20f726aaa6402ef50eee

Now, we can use `docker logs` to check the logs of the container.

###Command :

    $ docker logs -f 91f4dc4b32b92e05aaf1ea90ec0a02a7d03d7c00248a20f726aaa6402ef50eee

###Output : 

    hello docker
    hello docker
    hello docker
    ...

## Stopping a running container

So any container that is started with the -d option does not stop unless explicitly stopped. So how do we go about doing this. Let us try to stop our infinite loop container.

###Command : 

    $ docker stop  91f4dc4b32b92e05aaf1ea90ec0a02a7d03d7c00248a20f726aaa6402ef50eee

###Output

    91f4dc4b32b9

We can also stop all running containers by running the command

    $ docker stop 'docker ps -q'

## Deleting containers

We can delete or remove containers that are either up or stopped. To do so, 

###Command : 

    $ docker rm 474464f23ea6

###Output : 

    91f4dc4b32b9

You can verify this by doing a docker ps -a and searching for the container you just deleted.

## Setting restart policy

Before Docker 1.2, there was an option to restart a container. In version 1.2 and the ones following it, it has been added to the `run` command, with flags which specify the restart policy.

## Getting privileged access

## Exposing a port

## Exec into running container

## Inspecting
