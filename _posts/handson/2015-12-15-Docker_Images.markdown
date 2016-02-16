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



##Creating an account with Docker Hub


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


        A repository can hold different versions of an image.Any number of public repositeries could be created for the images.By default, one private repository is provided to the users.One can buy more private repositories.


 Docker Hub account could be created either by visiting the website or from command line.

1.Visit following website : https://hub.docker.com


2.For creating an account through command line : 

  $ docker login





##Creating an image from Container





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


>CONTAINER_ID	   |  IMAGE  |     COMMAND    |	   CREATED      |      STATUS       |   PORTS   |   NAMES   |
   ------            -------    ------------     ----------         -----------       --------    --------
>3b7d8fcd0a1d      |  ubuntu |    "/bin/bash" |    46 hours ago |    Up 11 minutes  |           |   jepsen  |
 
 
  $ docker images

 


 REPOSITORY                TAG                 IMAGE ID            CREATED             VIRTUAL SIZE

shradha09/ubuntu          httpd               a47606b00f39        2 minutes ago          187.9 MB
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


  IMAGE 	   CREATED	  CREATED BY	             SIZE	  COMMENT
		

 0dd2462fdefe     4 hours ago	   /bin/bash	              0 B	  Ubuntu with HTTPD package 
 
 6cc0fc2a5ee3     3 weeks ago 	 /bin/sh -c #(nop)            0 B           
                                 CMD ["/bin/bash"]	        

 f80999a1f330	  3 weeks ago	 /bin/sh -c sed -i            1.895 KB
                                's/^#\s*\(deb.*universe\)$/	
			
 92ec6d044cb3	  3 weeks ago	/bin/sh -c #(nop)             187.7 MB
                                 ADD file:7ce20ce3daa6af21db	

  #####Metadata- Through Metadata of an image Docker will come to know about how the image is been created, using *history* command, by looking at the information provided in the metadata recursively, reaching origin.

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


  $ docker save [-o |--output=""] IMAGE  [:TAG]


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


 ~~ Working of Docker build ~~


Docker build, build images from a Dockerfile and a “context”. A build’s context is the files located in the specified PATH or URL. The build process can refer to any of the files in the context. 

For example, your build can use an ADD instruction to reference a file in the context.

This is done by Docker Deamon and the entire context is transferred to it, this is the reason we see *Sending build context to Docker Deamon 2.048KB* message.

We could also include a *.dockerignore* command in the current working directory with the files and directories, then those files will be ignored by the deamon.

   After executing each instruction, Docker commits every intermediate images and run the container with the next instruction and commits, it keeps on removing the intermediate container in the previous step to make space for the new container after reading the instructions.


~~ Additional ~~

To look for intermediate layers of an image, specify -a option with docker images : 

  $ docker images -a 
 
         
|  REPOSITORY     |   TAG      | IMAGE ID    |   CREATE D     | VIRTUAL SIZE
 :------------:    :---------:  :---------:   :-------------:  :-----------:
|docker.io/ubuntu |   latest   |6cc0fc2a5ee3 |  3 weeks ago   |   187.9 MB
|<none>           |   <none>   |2ef91804894a |  3 weeks ago   |   187.9 MB
|<none>           |   <none>   |92ec6d044cb3 |  3 weeks ago   |   187.7 MB



### Dockerfile Documnetation 


The format of Dockerfile is as follows :
 
~~ INSTRUCTION ARGUMENTS ~~

Instructions are always given in Uppercase but they are not case sensitive.They are evaluated in order. 
A *#* in the beginning is treated as the comment. 

Types of Instructions  : 

a.) FROM- This must be the first instruction to any Dockerfile, which sets the base image for other subsequent instructions.
     
           FROM <image>

Image with tag could also be placed : 


 FROM <image>:<tag>

Multiple FORM instructions are possible in single Dockerfile to create several different images. 

* If only the image name is given in the Dockerfile such as Ubuntu or centos, the images will be downloaded from Default Docker Registry i.e from Docker Hub. *

If you want to use the private or third-party images, then following command has to be used:

Syntax :  #### [registry_hostname[:port]/][user_name/](repository_name:version_tag)

Example : registry_hostname:5000/shradha09/centos7:httpd


b.) MAINTAINER- It sets the author name for the generated image.

            MAINTAINER <name>

c.) RUN- We can execute the RUN in following two ways : 
   
    1. Running it in shell (sh -c)

    RUN <command> <example1>....<paramN>


    2. Directly running the executable : 

     RUN ["executable", "example1",....,"exampleN"
    
As in Docker we create layers over top of other layers to make the resulting image. Through *run* command we are actually committing layer on top of earlier committed layers. A container can be started form any of the committed layers . 

  By default, Docker tries to cache the layers committed by different RUN instructions, so that it can be use in subsequent builds.This behaviour could be turned off by using --no-cache flag while the image is build.
  Once the parameters are defined with the ENTRYPOINT instruction,they cannot be overwritten at runtime.However, ENTRYPOINT can be used as CMD, if parameters are needed to be changed.


d.) LABEL- Docker 1.6 added a new feature to the attached arbitrary key-value pair to Docker images and containers.To give a label to            an image, LABEL instruction is used in the Dockerfile as LABEL distro=ubuntu.



e.) CMD- The CMD instruction provides a default executable while starting a container. If the CMD instruction does not have an executable, then it will provide arguments to ENTRYPOINT.
          Given how much easier it is to override the CMD, the recommendation is use CMD in your Dockerfile when you want the user of your image to have the flexibility to run whichever executable they choose when starting the container. 

      CMD ["exectable","example1",....,"exampleN"]

      CMD ["example1",....,"exampleN"]
 
      CMD <command> <example1> ... <example2>

Note: Only one CMD instruction is allowed in a Dockerfile. If more than one is specified, then only the last one will be read.




f.) ENTRYPOINT- Both ENTRYPOINT and CMD gives a way to identify which executable should be run when a container is started from an image.Also, if you want your image to be runnable (without additional docker run command line arguments) you must specify an ENTRYPOINT or CMD.
 
      ENTRYPOINT ["exectable","example1",....,"exampleN"]
    
      ENTRYPOINT ["example1",....,"exampleN"]


    The ENTRYPOINT or CMD that are specified in the Dockerfile identify the default executable for the image. However, the user has the option to override either of these values at run time.
    Once the parameters are defined with the ENTRYPOINT instruction,they cannot be overwritten at runtime.However, ENTRYPOINT can be used as CMD, if parameters are needed to be changed.


 
 
g.)EXPOSE-  The EXPOSE instruction informs Docker that the container listens on the specified network ports at runtime. EXPOSE does not make the ports of the container accessible to the host. To do that, you must use either the -p flag to publish a range of ports or the -P flag to publish all of the exposed ports. You can expose one port number and publish it externally under another number.

 

        -P         : Publish all exposed ports to the host interfaces
        
        -p=[]      : Publish a container᾿s port or a range of ports to the host
        
                     format: ip:hostPort:containerPort | ip::containerPort | hostPort:containerPort | containerPort
                     Both hostPort and containerPort can be specified as a range of ports.
              
    
Syntax :  EXPOSE <port> [<port>...]


h.)ENV- The ENV instruction sets the environment variable *<key>* to the value *<value>*. This value will be in the environment of all “descendent” Dockerfile commands and can be replaced inline in many as well. It will be passed all the future instructions and will persist when a container is run from the resulting image : 

         ENV <key> <value>

The ENV instruction has two forms. The first form, ENV <key> <value>, will set a single variable to a value. The entire string after the first space will be treated as the <value> - including characters such as spaces and quotes.

The second form, ENV <key>=<value> ..., allows for multiple variables to be set at one time. Notice that the second form uses the equals sign (=) in the syntax, while the first form does not. 



i.)ADD- This copies files from source to destination
 
ADD has two forms:

    ADD <src>... <dest>
    ADD ["<src>",... "<dest>"] (this form is required for paths containing whitespace)

The ADD instruction copies new files, directories or remote file URLs from <src> and adds them to the filesystem of the container at the path <dest>.

Multiple <src> resource may be specified but if they are files or directories then they must be relative to the source directory that is being built (the context of the build).
       

~~ <src> : This must be the file or directory inside the build directory from which we are building an image, which is also called the comtext of the build.A source can be a remote URL as well.

~~ <dest> : This must be the absolute path inside the container in which the files/directory from the source will be copied.


j.)COPY- COPY has two forms:

    COPY <src>... <dest>
    COPY ["<src>",... "<dest>"] (this form is required for paths containing whitespace)

The COPY instruction copies new files or directories from <src> and adds them to the filesystem of the container at the path <dest>.

Multiple <src> resource may be specified but they must be relative to the source directory that is being built (the context of the build).



k.)VOLUME- This instruction will create a mount point with the given name and flag it as mounting the external volume using the following command : 

   VOLUME ["/data]

 Alternatively used code : 

   VOLUME /data



l.)USER- This sets the username for any of the following run instructions using the following syntax :
  
         USER <username>/<UID>
         
   The USER instruction sets the user name or UID to use when running the image and for any RUN, CMD and ENTRYPOINT instructions that follow it in the Dockerfile.

        USER daemon


m.)WORKDIR- The WORKDIR instruction sets the working directory for any RUN, CMD, ENTRYPOINT, COPY and ADD instructions that follow it in the Dockerfile.

          WORKDIR /path/to/workdir


It can be used multiple times in the one Dockerfile. If a relative path is provided, it will be relative to the path of the previous WORKDIR instruction. For example:

WORKDIR /a
WORKDIR b
WORKDIR c
RUN pwd

The output of the final pwd command in this Dockerfile would be /a/b/c.

The WORKDIR instruction can resolve environment variables previously set using ENV. You can only use environment variables explicitly set in the Dockerfile. For example:

ENV DIRPATH /path
WORKDIR $DIRPATH/$DIRNAME

The output of the final pwd command in this Dockerfile would be /path/$DIRNAME


n.)ONBUILD- he ONBUILD instruction adds to the image a trigger instruction to be executed at a later time, when the image is used as the base for another build. The trigger will be executed in the context of the downstream build, as if it had been inserted immediately after the FROM instruction in the downstream Dockerfile.

Any build instruction can be registered as a trigger.

This is useful if you are building an image which will be used as a base to build other images.

                  ONBUILD [INSTRUCTION]

                     
## Building a Jenkins image - Dockerfile example

Dockerfile contents for Jenkin : 
                                                                                |FROM fedora
|MAINTAINER http://fedoraproject.org/wiki/Cloud

|RUN dnf -y update && dnf clean all
|RUN dnf -y install jenkins java initscripts supervisor && dnf clean all

|EXPOSE 8080

|RUN rm -rf /var/run/jenkins.pid

|VOLUME ["/root/.jenkins"]

|ADD ./supervisord.conf /etc/supervisord.conf

|CMD [ "supervisord", "-n" ]
 

Other supporting files in Repository : 

-README.md: This is the README file.

-LICENSE: This is GNU Public License.

-supervisord.conf: This file have  Configuration files.

 
For building new Image ,use following *build* command :

  $ docker build -t fedora/jenkins

The build process takes the base image of Jenkins, installs given RUN from the Dockerfile.


## Setting up Private Index/Registry

The public Docker Registry is available at Docker Hub, through which the users can push/pull images.We can also host a private registry either on a local environment or on the cloud, following are the ways by which this can be done : 

1. Use the Private Docker registry from Docker Hub.

2. Build an image from Dockerfile and run a registry container. 

3.Configure the distribution-specfic package such as ------ , which provides thedocker-registry package which can be installed and configured.

 The easiest way to set it up is through the registry container itself.


#### Steps to be followed : 

a. To run the registry on the container :

    $ docker run -p 5000 samalba/docker-registry
  
b. To test the newly created registry, perform following steps :

   1. Start a container by using the command :


          $ W='docker run -d -i jenkins /bin/bash'


   2. Make some changes if needed and commit those changes to the local repository : 

        $ docker commit $W jenkins


   3. To push the image use following command : 
           

       $ docker push [REGISTRY_HOST_NAME]:[PORT_NUMBER]/[USERNAME]/[IMAGE_NAME]


   4. For Pulling image from local registry, use following command : 

        
       $ docker pull [REGISTRY_HOST_NAME]:[PORT_NUMBER]/[USERNAME]/[IMAGE_NAME]
 

Note : The registry can be configured on any existing servers, steps to do this       are available in following github link :
 
   https://github.com/docker/docker-registry

To understand how the registry images are created and how different configuration options are given,*Dockerfile* of docker-registry can be visited .


## Automated Builds - With GitHub OR BitBucket
 
 Images can also be build automatically from a *build context* stored in a repository. 
 A *build context* is a Dockerfile or any file at any specified location. 
 For an automated build,the build  context is a repository containing a Dockerfile.Automated Builds have several advantages:

a.)Images built in this way are built exactly as specified.
    
b.)The Dockerfile is available to anyone with access to your Docker Hub repository.
   
c.) You repository is kept up-to-date with code changes automatically.

Automated Builds are supported for both public and private repositories on both GitHub and Bitbucket.

~ Use of automated builds requires that you have an account on Docker Hub and on the hosted repository provider (GitHub or Bitbucket).

-To view settings for GitHub or BitBucket account linkage, from your Docker Hub account choose :

    #####Profile > Settings > Linked Accounts & Services.
 
#### Link to a hosted Repository 

1.Log onto http://hub.docker.com/ 

2.Navigate to 
 
     Profile > Settings > Linked Accounts & Services.

3. Click the service to be linked ( Either GitHub or BitBucket )

  The system prompts to choose between Public & Private and Limited Access. The Public and Private connection type is required if we want to use the Automated Builds.

         Press *Select* under Public and Private connection type.

4.After you grant access to your code repository, the system returns you to Docker Hub and the link is complete.


#### Creating an Automated Build


Automated build repositories rely entirely on the integration with your code repository. You cannot push to these image repositories using the docker push command. You can only change the image by committing to your code repository. If you want to do both, docker push and an automated build, you would create a regular repository for the docker push and an automated build repository.

##### 1. Select Create > Create Automated Build from Docker Hub.
      The system prompts you with a list of User/Organizations and code repositories.

##### 2.Select from the User/Organizations.

##### 3.Optionally, type to filter the repository list.

##### 4.Pick the project to build.

      The system displays the Create Automated Build dialog.


    The dialog assumes some defaults which you can customize. By default, Docker builds images for each branch in your repository. It assumes the Dockerfile lives at the root of the source. When it builds an image,Docker tags it with the branch name.

##### 5.Automated build could also be customized by using the link provided, which says: *Click here to customize* behaviour.

        Note: To specify which code branches or tags to build from. You can add new configurations by clicking the + (plus sign) on right hand side .
           
      
##### 6.The system displays the home page for AUTOMATED BUILD.


When a GitHub repository is added for automated build, GitHub enables the Docker service for that repository.

 For more configurations, view : Settings > Applications 

Whenever we make any changes to the GitHub repository,such as commits,an automated build gets trigerred using the Dockerfile that resides in the GitHub repository.


~~ Details for Automated Build : 

Select Automated Build Container or Go to Details of the container, following details are found on the new opened web page : 

-Repo Info: Contains description about the repository, two types of descriptions could be read, short and full description.


-Tags: Repository tags are similar to docker image tags, generally  we use 'latest' as tag, other used tags      are httpd, version number etc.


-Dockerfile: A Dockerfile is a text document that contains all the commands a user could call on the command line to assemble an image. Using docker build users can create an automated build that executes several command-line instructions in succession.


-Build Details: It consists of Status, Tag, Created, Last Updated , such information about the containers. 


-Build Settings: The Build Settings page allows you to manage your existing automated build configurations and add new ones. By default, when new code is merged into your source repository, it triggers a build of your DockerHub image.


-Collaborators: Users contributing in the repositories.
 

-Webhooks: One can use a webhook to cause an action in another application in response to an event in your automated build repository. Currently, the webhook fires when an image is built in or a new image tag is added to the automated build repository.
           Webhook specifies a target URL and a JSON payload to deliver. The webhook generates an HTTP POST that delivers a JSON payload.


-Settings: Consists of two sections, changing a Public repository, Private and an option for deleting the existing repository.
  
  CAUTION: Deleting a repository will ####destroy all images stored in it,this action is not reverible.



## Creating the base image- using Supermin


To build a base image, we need to have a distribution-specific base system installed into a directory, which can later be imported as an image to Docker.
  With *chroot* utility, we can fake a directory as the root filesystem and then put all the necessary files in it, before importing it as Docker image.

Supermin and Debootstrap are the tools that can help make the faking of root filesystem easier.

Supermin- Tool for creating and building supermin appliances.

~~ Command Syntax for Prepare ~~

  $ supermin --prepare -o OUTPUTDIR PACKAGE [PACKAGE ...]

  $ supermin --build -o OUTPUTDIR -f chroot|ext2 INPUT [INPUT ...]


Supermin is a tool for building supermin appliances.These are tinyappliances (similar to virtual machines), usually around 100KB in
size, which get fully instantiated on-the-fly in a fraction of a
second when you need to boot one of them.
   This program was previously known as Febootstrap.

Supermin does not need to be run as root, and generally should notbe run as root.It does not affect the host system or the packages installed on the host system.

~~ Two modes of Supermin ~~

 _PREAPARE MODE_

  *--prepare* creates the tiny supermin appliance in the given output directory. Give the list of packages that are to be installed, and supermin will automatically find all the dependencies. The listof packages has to be installed on the host machine . 

Example : $ supermin --prepare bash coreutils -o supermin.d

The above command creates a supermin appliance containing the packages "bash" and "coreutils". Specifically,it creates files in directory "supermin.d". This directory is known as Supermin Appliance.

*prepare* mode puts all the requested packages with their dependencies inside a directory without copying the host OS specific files

~~ Command Syntax ~~

   $ supermin --prepare -o OUTPUTDIR PACKAGE [PACKAGE ...]

  Example : $ supermin --prepare bash coreutils -o ubuntu



 _BUILD MODE_

 
  *--build* was previously a separate program called "supermin-helper" 
 
~~ Command Syntax ~~

  $ supermin --build -o OUTPUTDIR -f chroot|ext2 INPUT [INPUT ...]

 Example: $ supermin --build --format chroot ubuntu -o ubuntu_image


 If we do *ls* on the output directory i.e ubuntu_image, we will find directory tree similar to any linux root filesystem :

 $ ls ubuntu_image

  bin	boot	dev	etc	home	lib	lib64	media	mnt	opt	proc	root	run	
  abin	srv	sys	tmp	usr	var	

~~ We can export the directory as docker image using following command:

  $ tar -C ubuntu_image/ -c . | docker import - skhare/ubuntu

f6f5f2dd61de2f1cca7475824df9af2ffc3d337ad0660d212c82c94441789426

~~ Using command *docker images* a new image with skhare/ubuntu as name will be seen.
 

In *build* mode, the supermin appliance created from the prepare mode gets converted into a bootable appliance with all the necessary files 
 Build will copy the required files/binaries from the host machineto the appliance directory,so the packages must be installed on the host machines that is to be used in the appliance.


## Creating the base image - using Debootstrap

Debootstrap is a tool which will install a Debian base system into a subdirectory of another, already installed system. It can also be installed and run from another operating system, so, for instance, you can use debootstrap to install Debian onto an unused partition. 


Install Debootstrap on Debian-based system using following command : 

  $ apt-get install debootstrap

Following commands can be used to create the base-image :

  $ debootstrap [OPTION...] SUITE TARGET [MIRROR [SCRIPT]]

- SUITE: It refers to the release code name.

- MIRROR: Refers to the respective repository

Example : Create a base image of fedora 23, using following steps :
 
 1. Create a directory, on which the OS is to be installed. Debootstrap creates the chroot environment to install a package : 

     $ mkdir deboot_chroot


 2. Use, *debootstrap*, install fedora 23 inside the directory that we created :
 
     $ debootstrap deboot ./deboot_chroot
     http://in.archive.fedora-23/fedora/


 3. We will see the directory tree to any linux root filesystem, inside the directory where OS is installed, same as we saw in Supermin.

     $ ls ./deboot_chroot

     bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr    var			    

 4.  Export the directory as a Docker image with following command : 
    
     $ tar -C deboot_chroot/-c . | docker import - skhare/fedora


 5. Look at the *docker images* output, a new image with skhare/fedora as name can be seen.
