---
layout: article
title: "Images"
categories: articles
modified: 2014-08-27T11:57:41-04:00
tags: [concept]
comments: true
ads: false
---

{% include toc.html %}

# Before we start container Operations

##Listing/ searching an image

As we know, we require images to start containers. These containers are available in docker registries, from where we can get them. But first, we need to know if the image we need is available in the registry.

So lets try searching an image. For the purpose of this tutorial, we will use a small, minimalistic image, but you are welcome to use any image of your choice.

###Command : 

    $ docker search tianon/true

###Output : 

    INDEX       NAME                    DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
    docker.io   docker.io/tianon/true   125 bytes total - nothing but "true" (perf...   72                   [OK]

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

Before Docker 1.2, there was an option to restart a container. In version 1.2 and the ones following it, it has been added to the `run` command, with flags which specify the restart policy. With this policy, we can do stuff like start a container on boot or if the container dies accidentally.

###Command : 

    $ docker run --restart=always -dit centos /bin/bash

The restart policy can take any of these 3 values : 

 - `no` : This does not start the container if it dies.
 - `on-failure` : This restarts the container if it fails with a non-zero exit code.
 - `always` : This always restarts the container without worrying about the exit code

## Getting privileged access

In Linux, privileges traditionally assigned to the superuser are divided into distinct units, known as capabilities (you van find out more by running `man capabilities` on your Linux machine), each of these capabilities can be individually enabled or disabled.

By default, Docker starts containers with limited capabilities. With privileged access inside the container, we give more capabilities to perform operations, normally done by root. For example, let us attempt to create a loop back device, while mounting a disk image.

###Commands : 

    $ docker run -it centos /bin/bash
    # dd if=/dev/zero of=mydisk.img bs=1M count=100 &> /dev/null
    # mkfs -t minix mydisk.img &> /dev/null
    # mount mydisk.img /mnt/

###Output : 

    mount: /mydisk.img: failed to setup loop device: No such file or directory

Now lets kill this container and then try that again by running the container in privileged mode.

###Commands : 

    $ docker run --privileged -it centos /bin/bash
    # dd if=/dev/zero of=mydisk.img bs=1M count=100 &> /dev/null
    # mkfs -t minix mydisk.img &> /dev/null
    # mount mydisk.img /mnt/
    # mount | grep mnt

###Output : 

    /mydisk.img on /mnt type minix (rw,relatime)

## Exposing a port

There are a number of ways by which, we can expose ports on a container  to the outside world. One of them, is through the `run` command. The other way is through the `--link` command  in a docker file. 

Here, we shall see  how to use `run` to expose ports.

###Command 

    $ docker run --expose=22 -it centos /bin/bash

> **Note :** You are encouraged to try this with a container that can make use of this, like an apache container.

## Exec into running container

If we wish to execute commands in a container that is already running, we can do so by injecting it into the container by using the exec option. First, let us start a container in the background. 

For our example, let us first pull and run an nginx container. If you have forgotten how to do so, please scroll upwards. 

Once you have started the nginx container using `docker run -dit nginx`, note down the ID and we can try to inject another command, say bash into the container. 

###Command :

    $ docker exec 9fc795a385f6 bash

###Output : 

You will be taken into the bash terminal of the container.

## Inspecting Containers

We might need to access containers configuration and other information for the purpose of debugging, automation and so on. For this purpose, we can use the `inspect` command. First of all, make sure you have a running container and know its ID. Now let us inspect it.

###Command

    $ docker inspect 606983e91847

###Output

    [
    {
        "Id": "606983e91847f13ab6b8332843f671f1fb5ec10ab193924d6a2a6ed915a3ac63",
        "Created": "2016-01-17T08:08:03.232356701Z",
        "Path": "bash",
        "Args": [],
        "State": {
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 5237,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2016-01-17T08:08:03.515789746Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "0f73ae75014f435e279d85ad31edc67e46c4a5d692b61840ff51e9d05f3b01ec",
        "NetworkSettings": {
            "Bridge": "",
            "EndpointID": "f591fa3e3018833e72e72634c06583ffba3ef9d9596180f3844689a2e53b0e7b",
    ...
            "Image": "centos",
            "Volumes": null,
            "VolumeDriver": "",
            "WorkingDir": "",
            "Entrypoint": null,
            "NetworkDisabled": false,
            "MacAddress": "",
            "OnBuild": null,
            "Labels": {}
        }
    }
    ]


Do not forget that you can continue learning more about this by trying out more examples on your own and referring the docker documentation.
