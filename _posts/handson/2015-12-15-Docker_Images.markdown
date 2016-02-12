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
 
| Containers: 30
| Images: 47
| Server Version: 1.9.1-fc23
| Storage Driver: devicemapper
| Pool Name: docker-253:0-1053518-pool
| Pool Blocksize: 65.54 kB
| Base Device Size: 107.4 GB
| Backing Filesystem: 
| Data file: /dev/loop0
| Metadata file: /dev/loop1
| Data Space Used: 2.213 GB
| Data Space Total: 107.4 GB
| Data Space Available: 66.01 GB
| Metadata Space Used: 4.379 MB
| Metadata Space Total: 2.147 GB
| Metadata Space Available: 2.143 GB
| Udev Sync Supported: true
| Deferred Removal Enabled: false
| Deferred Deletion Enabled: false
| Deferred Deleted Device Count: 0
| Data loop file: /var/lib/docker/devicemapper/devicemapper/data
| Metadata loop file: /var/lib/docker/devicemapper/devicemapper/metadata
| Library Version: 1.02.109 (2015-09-22)
| Execution Driver: native-0.2
| Logging Driver: journald
| Kernel Version: 4.3.3-303.fc23.x86_64
| Operating System: Fedora 23 (Workstation Edition)
| CPUs: 2
| Total Memory: 1.821 GiB
| Name: localhost.localdomain
| ID: GEFR:KSZ5:SHLG:BIT6:FIFU:AOJG:7LXK:RQ22:HUXF:FTJS:Q4FY:U2LY
| Username: shradha09
| Registry: https://index.docker.io/v1/


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
    
   $ docker commit -a ``|``--author[=""] -m``|``--message[=""]   CONTAINER   [REPOSITORY[:TAG]]

b.Now, start a container and create/ modify some files on it by using *install httpd* package : 


  $ docker run -i -t ubuntu /bin/bash

  root@4312ee7658e7:/# dnf install -y httpd


c.On a new terminal, create a new image by giving following command:

   $ docker commit -a "Shradha" -m "Ubuntu with HTTPD package" 3b7d8fcd0a1d shradha09/Ubuntu:httpd


Output : 

  $ docker ps


CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES

3b7d8fcd0a1d        ubuntu              "/bin/bash"         46 hours ago        Up 11 minutes                           romantic_jepsen
 
 
  $ docker images

  REPOSITORY                TAG                 IMAGE ID            CREATED             VIRTUAL SIZE

shradha09/ubuntu               httpd               a47606b00f39        2 minutes ago          187.9 MB
yu/fedora                 httpd               d33f23884c55        6 days ago             362.2 MB
docker.io/docker          latest              e90df13f8efb        2 weeks ago            36.87 MB
docker.io/ubuntu          latest              6cc0fc2a5ee3        3 weeks ago            187.9 MB

A new image is being committed to the local repository with the name shradha09/ubuntu as a name and *httpd* as a tag.


Explanation :   When a container is started, a read/write filesystem layer will be created on top of existing image layers from where the container has started and when the package is installed some files are created or existing files are modified in that layer. All these changesmade are to be saved explicitly otherwise on deleting or stopping the container all the modifications are lost. 
                To overcome this loss, commit command is used when a new layer is created with the changes that took place since the container is startedand get saved in the container's backend storage driver.


###Important

1.To look which files are changed or modified since the container has started use the following command : 

  Syntax :  $ docker diff [CONTAINER_ID]

  Example : $ docker diff 3b7d8fcd0a1d
             
            C /run
            A /run/secrets

Explanation for prefixes before each generated result : 
  
 1. A: This is for when a file/directory has been Added.
 2. C: This is for when a file/directory has been Modified.
 3. D: This is for when a file/directory has been Deleted.
 
2.By default a container gets paused during commit, one can change this behavior by passing --pause=false to commit. 

3.For more options use: $ docker commit --help



## Publishing an image to the Registry

 
Assuming an image is already been created suitable for development environment. This image could be shared by using tar ball or uploaded in a centralised location from where any user can pull that image.Central location can be either Publicor Private registry. The uploading of image is done by using *docker push* command.

### A local registry has to be set up for pushing the images/repositeries locally.

Syntax : $ docker push NAME[:TAG]


This command will use the username and registry shown in the *docker info* command to push the images.Here, the username will be "abcd" and registry will be "https://index.docker.io/v1


For pushing the image we created, use following command:

   $ docker push abcd/ubuntu:httpd


|The push refers to a repository [docker.io/abcd/ubuntu] (len: 1)
|0dd2462fdefe: Pushed 
|6cc0fc2a5ee3: Pushed 
|f80999a1f330: Pushed 
|2ef91804894a: Pushed 
|92ec6d044cb3: Pushed 
|httpd: digest: sha256:794def2e25f46a69c8f9c2c35704566dc573be4857a0f24b4cf02820b|304d0a7 size: 8101

 
Suppose, you want to push the image to the local registry, hosted on host named localhost_registry. For this, the image has to be tagged with registry's host name or IP address with port number on which the registry is running and then the image is pushed.


  $ docker tag [-f``|``--force[=false]  IMAGE  [REGISTRYHOST/]  [USERNAME/]NAME[:TAG] 

 
  $ docker push [REGISTORYHOST/]  [USERNAME/]NAME[:TAG]  


Example : Suppose our registry is configured on some website say xyz.something.com, then for image tagging following command is used : 
       
 
   $ docker tag abcd/ubuntu:httpd xyz.something.com:5000/abcd/ubuntu:httpd


### Push the image : 


   $ docker push xyz.something.com:5000/abcd/ubuntu:httpd

Listing of all the intermediate layers are done which are required to make the specific image.Searches for the layers present already and copies only those layers which are not present in the registry with the metadata required to build theimage.



## Looking at the history of an Image


*docker history* command is used to find all the intermediate layers by which the image is created.


Syntax : 
         $ docker history [OPTIONS] IMAGE


Example : 
         $ docker history abcd/ubuntu:httpd


|  IMAGE 	|   CREATED	| CREATED BY	|    SIZE	|  COMMENT
|		|		|		|		|
 :-------------: :-------------: :-------------: :-------------:
|0dd2462fdefe   | 4 hours ago	|  /bin/bash	|     0 B	|  Ubuntu with HTTPD package 
|6cc0fc2a5ee3   | 3 weeks ago 	|/bin/sh -c #(nop)    0 B           
                                 CMD ["/bin/bash"]	        

|f80999a1f330	| 3 weeks ago	|/bin/sh -c sed -i   1.895 KB
                                's/^#\s*\(deb.*universe\)$/	
|		|		|		|		|	
|92ec6d044cb3	| 3 weeks ago	|/bin/sh -c #(nop)   187.7 MB
                                 ADD file:7ce20ce3daa6af21db	|

#### Metadata- Through Metadata of an image Docker will come to know about how the image is been created, using *history* command, by looking at the information provided in the metadata recursively, reaching origin.

~~ By *inspect* command, commit messages for all the layers could be obtained.

Example : $ docker inspect --format='{{.Comment}}' shradha09/ubuntu:httpd
            
          Ubuntu with HTTPD package



## Deleting an Image 


To remove the image from the host, *docker rmi* command is used, it will remove the images from host not from the registry.

~~ Syntax for Removing Image : 
   
   $ docker rmi [OPTIONS]  IMAGE  [IMAGE...]


~~ Example :  
    
   $ docker rmi abcd/ubuntu:httpd

   Untagged : abcd/ubuntu:httpd
   
   Deleted  : 6579dfa51537ac7a94f3ab03a76d5c5f06bceb2f2eae3e4e42a621620b91e57f



~~ Additional Info ~~

All the containers and images could be removed, but prove to be destructive,keeping this in mind, here are few commands that could remove images and containers:

1. To stop all containers :
  
   $ docker stop 'docker ps -q'

2. To delete all containers : 
 
   $ docker rm 'docker ps -a -q'

3. To delete all images : 
   
   $ docker rmi 'docker images -q'




## Exporting an Image  

If due to some restrictive policies which do not allow to use images from publicdomain. In such cases, the images could be shared using tarballs, which later can be imported on another system by using *docker save* command.

Note: Pull or import one or more Docker images on the docker host. 

~~ Syntax to save the image in tar file : 


  $ docker save [-o``|``--output=""] IMAGE  [:TAG]


~~ Example, creating a tar file for Ubuntu, run following command :


  $ docker save --output=ubuntu.tar ubuntu

Note: If the tag name is specified with the image we want to export, such as          unbuntu:latest, then only the layers related to that tag will get exported.

~~ Additional Info ~~

If --output or -o is not used, then the output will be streamed to STDOUT : 


Example : $ docker save ubuntu:latest > ubuntu-latest.tar


Similarly, the contents of the container's filesystem can be exported using the following command : 

 $ docker export CONTAINER > conatinerABC.tar



## Importing an image

In order to get a local copy of a image, we either need to pull it from the registry or import it from the already exported image, by using *docker import* command, we import an exported image.
An accessible exported Docker image is needed for that.


Syntax : 1. To import an image :
         
           $ docker import URL ``|`` [REPOSITORY[:TAG]]

Example :  $ cat ubuntu-latest.tar ``|`` docker import - ubuntu:latest


Alternatively :  $ docker import http://xyz.com/xyz.tar xyz/image


These commands will first create an empty filesystem and then import the contents.
                


## Building images using Dockerfiles

Using the *docker commit* command is a pretty simple way of extending an image but, it will be slow and complicated and therefore inefficient also sharing of image for development process among the team will become difficult. 
   A new command that could overcome the above problem is  *docker build*, for building new images from scratch.

 To do this you create a *Dockerfile* that contains a set of instructions that tell Docker how to build an image, Docker builder will read instructions from Dockerfile which is a text file and executes them in sequence.

### Steps :

1. Create a Directory 
  
   $ mkdir image_example
   
   $ cd image_example

2. Create file named Dockerfile with following contents inside the directory: 

   $ cat Dockerfile
   
   # Pick up the base image
    FROM ubuntu
   # Add author name
   MAINTAINER shradha09
   # Add the command to run at the start of container CMD date
    

### Run the following command inside the directory, to build the image : 

           $ docker build .

OUTPUT : | Sending build context to Docker daemon 2.048 kB
         | Step 1 : FROM ubuntu
         |    ---> 6cc0fc2a5ee3
         | Step 2 : MAINTAINER shradha09
         |    ---> Running in 41a3ad61485d
         |    ---> 15e643a46289
         | Removing intermediate container 41a3ad61485d
         | Step 3 : CMD date
         |    ---> Running in 15600c5596e2
         |    ---> 64910446ed6c
         | Removing intermediate container 15600c5596e2
         | Successfully built 64910446ed6c


Note : This build is run by the Docker deamon,not by the CLI. The build process first of all will send the entire context(recursively) to the deamon. 


#### We can also specify any repository or tag name while building the image with -t option as follows : 

   $ docker build -t ubuntu/abc

OUTPUT :| Sending build context to Docker daemon 2.048 kB
        | Step 1 : FROM ubuntu
	|     ---> 6cc0fc2a5ee3
	| Step 2 : MAINTAINER shradha09
	|     ---> Using cache
 	|     ---> 15e643a46289
	| Step 3 : CMD date
	|      ---> Using cache
	|      ---> 64910446ed6c
   	| Successfully built 64910446ed6c
 
The above command will give different output, here we are using cache after each instruction. Docker tries to save the intermediate images and use them in subsequent builds to accelerate the build process.
   If we don't want to cache the intermediate images, then add *--no-cache* option with docker build.


 
 




         
























## Building an Apache image - Dockerfile example
## Building an memcached image - Dockerfile example
## Building Wordpress image - Dockerfile example
## Setting up private Index/Registry
## Automated Builds - With GitHub and BitBucket
## Creating the base image - using supermin
## Creating the base image - using Debootstrap
## Visualizing dependencies between layers




