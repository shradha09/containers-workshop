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


## Creating an image on the container
























































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




