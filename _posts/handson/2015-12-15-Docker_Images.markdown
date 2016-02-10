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



## Creating an account with Docker Hub

Docker Hub is like GitHub for images. It is a public registry on which both public and private images could be hosted, shared and collaborated with others. It has integration with GitHub, Bitbukcket and could trigger automated build.

Information regarding docker system could be gathered by using following command: 

 $ docker info
 
 Containers: 30
Images: 47
Server Version: 1.9.1-fc23
Storage Driver: devicemapper
 Pool Name: docker-253:0-1053518-pool
 Pool Blocksize: 65.54 kB
 Base Device Size: 107.4 GB
 Backing Filesystem: 
 Data file: /dev/loop0
 Metadata file: /dev/loop1
 Data Space Used: 2.213 GB
 Data Space Total: 107.4 GB
 Data Space Available: 66.01 GB
 Metadata Space Used: 4.379 MB
 Metadata Space Total: 2.147 GB
 Metadata Space Available: 2.143 GB
 Udev Sync Supported: true
 Deferred Removal Enabled: false
 Deferred Deletion Enabled: false
 Deferred Deleted Device Count: 0
 Data loop file: /var/lib/docker/devicemapper/devicemapper/data
 Metadata loop file: /var/lib/docker/devicemapper/devicemapper/metadata
 Library Version: 1.02.109 (2015-09-22)
Execution Driver: native-0.2
Logging Driver: journald
Kernel Version: 4.3.3-303.fc23.x86_64
Operating System: Fedora 23 (Workstation Edition)
CPUs: 2
Total Memory: 1.821 GiB
Name: localhost.localdomain
ID: GEFR:KSZ5:SHLG:BIT6:FIFU:AOJG:7LXK:RQ22:HUXF:FTJS:Q4FY:U2LY


A repository can hold different versions of an image.Any number of public repositeries could be created for the images.
By default, one private repository is provided to the users.One can buy more private repositories.


Docker Hub account could be created either by visiting the website or from command line.

1.Visit following website : https://hub.docker.com


2.For creating an account through command line : 

  $ docker login


## Creating an image from Container

There are two ways of creating and updating an image : 

1.You can update a container created from an image and commit the results to an image.

2.You can use a Dockerfile to specify instructions to create an image.


~ Creating image by manually committing layers ~

As soon as a container is started, a read/write layer gets attached to it. This layer will get destroyed if not saved.Following explanation will show how to save that layer and make a new image from running or stopped container by using *docker commit* command.


a. For committing following command syntax is used :
    
   $ docker commit -a|--author[=""] -m|--message[=""] CONTAINER [REPOSITORY[:TAG]]

b.Now, start a container and create/ modify some files on it by using *install httpd* package : 


  $ docker run -i -t ubuntu /bin/bash
  root@4312ee7658e7:/# dnf install -y httpd


c.On a new terminal, create a new image by giving following command:

   $ docker commit -a "ABC" -m "Ubuntu with HTTPD package" 3b7d8fcd0a1d ABCD/Ubuntu:httpd


Output : 

  $ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
3b7d8fcd0a1d        ubuntu              "/bin/bash"         46 hours ago        Up 11 minutes                           romantic_jepsen
 
 
  $ docker images
  REPOSITORY                TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
ABCD/ubuntu                httpd               a47606b00f39        2 minutes ago          187.9 MB
yu/fedora                 httpd               d33f23884c55        6 days ago             362.2 MB
docker.io/docker          latest              e90df13f8efb        2 weeks ago            36.87 MB
docker.io/ubuntu          latest              6cc0fc2a5ee3        3 weeks ago            187.9 MB

A new image is being committed to the local repository with the name ABCD/ubuntu as a name and *httpd* as a tag.





































 
























































## Creating an image from container
## Pushing the image to Registry
## Looking at the history on an image
## Deleting an image
## Exporting an image
## Importing an image
## Building images using Dockerfiles
## Building an Apache image - Dockerfile example
## Building an memcached image - Dockerfile example
## Building Wordpress image - Dockerfile example
## Setting up private Index/Registry
## Automated Builds - With GitHub and BitBucket
## Creating the base image - using supermin
## Creating the base image - using Debootstrap
## Visualizing dependencies between layers




