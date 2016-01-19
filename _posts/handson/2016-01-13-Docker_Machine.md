---
layout: article
title: "The Docker Machine"
categories: articles
modified: 
tags: []
comments: true
ads: false
---

{% include toc.html %}

#What is it?
According to the docker documentation, a [docker machine](https://docs.docker.com/machine/ "Docker Machine Documentation.") **lets you create Docker hosts  on your computer, on cloud providers, and inside your own data center. It automatically creates hosts, installs Docker on them, then configures the docker client to talk to them. A “machine” is the combination of a Docker host and a configured client**.

Once you create one or more Docker hosts, Docker Machine supplies a number of commands for managing them. Using these commands you can

 - start, inspect, stop, and restart a host
 - upgrade the Docker client and daemon
 - configure a Docker client to talk to your host

#How it works?

Docker Machine allows you to provision Docker on virtual machines that reside either on your local system or on a cloud provider. Docker Machine creates a host on a VM and you use the Docker Engine client as needed to build images and create containers on the host.

To create a virtual machine, you supply Docker Machine with the name of the driver you want use. The driver represents the virtual environment.
You can get a list of [supported drivers here](https://docs.docker.com/machine/drivers/ "Supported docker machine drivers").

Since Docker runs on Linux, each VM that Docker Machine provisions relies on a base operating system. For convenience, there are default base operating systems. For the Oracle Virtual Box driver, this base operating system is the boot2docker.iso. For drivers used to connect to cloud providers, the base operating system is Ubuntu 12.04+. You can change this default when you create a machine. You can see the list of [supported operating systems here](https://docs.docker.com/machine/drivers/os-base/ "Docker machine supported OSes").

#Docker Machine Lab

Now, lets do some docker machine practice. We shall cover the basics here to get to started. For more detailed reference, check out the [docker machine command documentation](https://docs.docker.com/machine/reference/ "Docker machine command documentation").

##Setting up the lab

First off, let us set up a lab environment needed to try out docker machine. We shall be using Virtualbox as the provider here. So make sure that it is available on your system. 

For linux users, **assuming** that you **already have** the standard **docker packages installed**, you can install docker machine by the following commands

    $ curl -L https://github.com/docker/machine/releases/download/v0.5.3/docker-machine_linux-amd64 >/bin/docker-machine
    $ chmod +x /bin/docker-machine

For a windows operating system or MacOS, you can download the docker toolbox containing the complete package, including virtual box [right here](https://www.docker.com/docker-toolbox "Docker toolbox link").

> **Note :** After installing the packages, do not forget to ensure the path has been set for easy functioning. For windows users, this will usually be "C:/Program Files/Docker Toolbox/" but may vary. Add it to the environment variable `PATH`. Same goes for the mac users as well.

For more detailed installation details, look into [docker install documentation](https://docs.docker.com/machine/install-machine/ "Installing docker machine on linux."). 

Once done, you can test your installation by 

###Command :

    $ docker-machine version

###Output :

docker-machine version 0.5.5, build 02c4254

##Creating the docker hosts

First step of course, is to create a docker hosts or two for the purpose of this lab.

###Command :  

    $ docker-machine create -d virtualbox docker-machine-1

###Output : 

    Running pre-create checks...
    Creating machine...
    (docker-machine-1) Copying C:\Users\{username here}\.docker\machine\cache\boot2docker.iso to C:\Users\{username here}\.docker\machine\machines\docker-machine-1\boot2docker.iso...
    (docker-machine-1) Creating VirtualBox VM...
    (docker-machine-1) Creating SSH key...
    (docker-machine-1) Starting VM...
    Waiting for machine to be running, this may take a few minutes...
    ...
    Docker is up and running!
    To see how to connect Docker to this machine, run: docker-machine env docker-machine-1

Along similar lines, let us create a second machine

###Command :

    $ docker-machine create -d virtualbox docker-machine-2

The output will be similar to the first one, with slight changes.

##Listing the docker machines

We can list the docker hosts presently connected to the docker-machine.

###Command : 

$ docker-machine ls

###Output : 

    NAME               ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER   ERRORS
    docker-machine-1   -        virtualbox   Running   tcp://192.168.99.102:2376           v1.9.1
    docker-machine-2   -        virtualbox   Running   tcp://192.168.99.103:2376           v1.9.1

##Setting a Docker Host as active

Before we can actually start controlling the docker machines with the docker client, we need to use docker-machine to set the active docker host. All docker commands will be executed on the currently active docker host.

###Command : 

####Linux : 

    $ eval "$(docker-machine env docker-machine-1)"

####Windows : 

    $ FOR /f "tokens=*" %i IN ('docker-machine env docker-machine-1') DO %i

You can verify if the `docker-machine-1` is the active docker host with

###Command : 

    $ docker-machine active

###Output : 

    docker-machine-1

##Communicating with and switching between active docker hosts

Now that we have some basics of how to work with docker machine, lets try working with our two docker hosts. 

> **Note :** Once docker-machine has set a **particular docker host** as **active** all standard **docker commands** get **redirected** to the **currently active docker host**.

Lets start by creating pulling a docker image on docker-machine-1

###Command : 

    $ docker pull busybox

Every time a docker machine sets up a host, it configures the docker daemon on the docker host to work on tls ([more on docker daemon and tls here](https://docs.docker.com/engine/articles/https/ "How TLS works with docker")) in the background. This generates tls certificates, copied to the system running docker machine. Those certificates are copied on the home directory of the userthat you can find in your. I have shown an example for a windows machine.

###Command

    $ ls C:\Users\USERNAME\.docker\machine\certs

###Output

    ca-key.pem  ca.pem  cert.pem  key.pem

Now lets confirm if the pull was successful

###Command : 

    $ docker images

###Output : 

    REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
    busybox             latest              fc0db02f3072        5 weeks ago         1.113 MB

Now, let us switch the active docker host. Just run the commands we ran above in the **Setting a docker host as active** section except replace `docker-machine-1` with `docker-machine-2`.

You can verify by again running

###Command : 

    $ docker-machine active

###Output : 

    docker-machine-2

Now lets see what images are available to us.

###Command : 

    $ docker images

###Output : 

    REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE

As can be seen, there are no images as this a a different docker host. You can cross verify by switching back to `docker-machine-1` and checking the images again.

Of course, this is just the beginning and you are encouraged to dig deeper into the world of containers...


