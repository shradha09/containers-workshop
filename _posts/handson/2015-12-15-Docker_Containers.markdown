---
latest: article
title: "Containers"
categories: articles
modified: 2014-08-27T11:58:41-04:00
tags: [concept]
comments: true
ads: false
---

{% include toc.html %}

# Before we start container Operations..


## Listing/ searching an image

As we know, we require images to start containers. These containers are available in docker registries, from where we can get them.Before that, we need to know if the image we need is available in the registry.

Let us try searching an image. For the purpose of this tutorial, we will use a small, minimalistic image, but you can use any image of your choice.

Command : 

    ```
    $ docker search "IMAGE_NAME"
    ```

Example : 
  
   ```  
   $ docker search ubuntu | head -n4
   ```

Output : 

    ```
    INDEX       NAME                                        DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
    docker.io   docker.io/ubuntu                            Ubuntu is a Debian-based Linux operating s...   3079      [OK]       
    docker.io   docker.io/ubuntu-upstart                    Upstart is an event-based replacement for ...   61        [OK]       
    docker.io   docker.io/torusware/speedus-ubuntu          Always updated official Ubuntu docker imag...   25                   [OK]
    ```

Above description points out whether the image is official or automated. STARS signfies how many people liked that image.

To list the images that got more than 20 stars and are automated, run the following command :

    ```
    $ docker search -s 20 --automated ubuntu
    ```

## Pulling an Image
 
After looking up for the image in the registry,we can pull it to the system from docker registry by running the following command :

Command : 

    ```
    $ docker pull "IMAGE_NAME [:Tag]
    ```

Example : 
    
    ```
    $ docker pull ubuntu
    ```       

  The *pull* command downloads all layers from the Docker registry, which are required to create the image locally.

*More about tags:*

Image tags group images of the same type. For example, Fedora can have images with tags such as feodra21 , fedora22, fedora23 etc. Following is used to pull an image with specific tag: 

    ```
    $ docker pull fedora:fedora22
    ```

By default,the image with latest tag gets pulled. To pull all images corresponding to all tags,use following command:
  
    ```
    $ docker pull --all-tags fedora
    ```


Output:

    ```
    Using default tag: latest
  
    $ docker pull fedora
  
    Trying to pull repository docker.io/tianon/true ... latest: Pulling from docker.io/library/fedora
    
    3bc7a2e07962: Pull complete
    bc84b1f88237: Pull complete
    Digest: sha256:0c678029118314264306b49c931d6dce678c8c88143252342a8614210bea4129
    Status: Downloaded newer image for docker.io/fedora:latest
    ```

## Listing images

Now before we use any image to create containers, we might want to check which containers are currently available on the system. These images might have been pulled from the registry, imported through the *docker* command, or created through docker files.

Note: Make sure that the docker daemon is already running on the host.

Command : 

    ```
    $ docker images
    ```

Output : 
  
    ```
    REPOSITORY              TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
    docker.io/ubuntu        latest              6cc0fc2a5ee3        9 hours ago         187.9 MB
    docker.io/jekyll/jekyll latest              40c03a1c6fbb        10 days ago         150.9 MB
    docker.io/nginx         latest              407195ab8b07        10 days ago         133.8 MB
    docker.io/busybox       latest              0f864637f229        2 weeks ago         2.43 MB
    docker.io/mysql         latest              6762f304c834        2 weeks ago         283.5 MB
    docker.io/centos        centos7             c8a648134623        3 weeks ago         172.3 MB
    ```

Note : The Docker client talks to the Docker server and gets the list of images at the server end.


# Container Operations

Key features of Images are known , now we can start with container basic operations:


## Starting a Container

Once we have images, we can use them to start the containers.In the following commands we will  start a container with the ubuntu:latest image.

*Syntax to run Container :*

   ```
   $ docker run [options] IMAGE[:TAG] [command] [ARG...]
   ```

*Command:*

   ```
   $ docker run -i -t --name=ubuntu ubuntu /bin/bash
   ```

*By default docker picks the image with the latest tag* :

1. The -i option starts the container in "interactive mode".

2. The -t option allocates the *psuedo-tty* and attaches it to the standard input

Note: Software that emulates Terminal hardware, handling input and output in the same way a physical device will do, so that the software connected is not aware that no real devices are attached.


We started a container from the ubuntu:latest image, attach psuedo-tty, name it ubuntu and ran the /bin/bash command. If the name is not specified,then a randomstring will be assigned as the name.


Also, if the image is not available locally, then it will get downloaded from the registry and then run. Docker will automatically run the *search* and *pull* command before running the *run* command.


##Working of Docker behind the Scenes... 


1.It will merge all the layers that make that image using Union fileSystem.

2.Allocates a unique ID to a container, which is referred to as container ID.

3.Allocates a filesystem and mounts a read/write layer for the container. Any changes made to this layer is temporary and will be discarded if not committed.

4.Allocates a network/bridge interface.

5.Assigns an IP address to the container.

6.Executes the process specified by the user.

Also, with the default Docker configuration , it creates a directory with the container's ID inside /var/lib/docker/containers, which has the container's specific information such as hostname, configuration details, logs and /etc/hosts.

*Additional Info*

1. To exit from the container use "ctrl+D or type exit". It is similar to exiting from a shell but this will stop the container.

2. The *run* command creates and start the container. With Docker 1.3 or later, it is possible just to create the container using the *create* command and run it later using the *start* command, shown in following example :

     ```
     $ ID=$ (docker create -i -t ubuntu bash)
     ```
         
     ```
     $ docker start -a -i $ID
     ```

The container can be started in the background and then we can attach to it whenever needed. We need to use the -d option to start the contianer in the background: 
  ( -d is used to Enable the debug mode.Default is false) 

  ```
  $docker run -d -i -t ubuntu /bin/bash
  6986b0367d5fd322db5aa90c180d3832be81cf1e294a1b5c222aac8a30691a19
  ```

The preceeding command returns the container ID of the contianer to which we can attach later, as follows:

    ```
    $ ID='docker run -d -i -t ubuntu /bin/bash
    ```
  
    ```
    $ docker attach $ID
    ```

In the preceding case, we chose /bin/bash to run inside the container.If we attach to the container, we will get an interactive shell.We can run a non-interactive process and run it in the background to make a daemonized container similar to the following:

  ```
   $ docker run -d ubuntu /bin/bash -c "while [1]; do echo hello docker; sleep 1; done"
  ```

To remove the container after it exits, start the contianer with the --rm option, as follows:

  ```
  $ docker run -rm ubuntu
  ```

As soon as the command executes, the container will be removed.

The --read-only option of the run command will mount the root filesystem in the read-onlymode:
  
  ```
  $ docker run --read-only -d -i -t ubuntu /bin/bash
  ```

   This option is very useful when we don't want users to accidently write content inside the container, which gets lost if the container is not committed or copied out or non-ephemeral storage such as volumes.

Custom labels can be given to containers, which can be used to group the containers based on labels.

## Listing

We can list both running and stopped containers.

To list the containers, run the following command:

Command 

   ```
   $ docker ps [OPTIONS]
   ```

Output : 

   ```
   CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
   c18653529e7e        centos              "/bin/bash"         8 seconds ago       Up 3 seconds                            cent
  fac59b475b1b        nginx               "/bin/bash"         About a minute ago  Up About a minute   80/tcp, 443/tcp     nginx
   6986b0367d5f        ubuntu              "/bin/bash"         35 minutes ago      Up 35 minutes                           nostalgic_jennings
   ```


The Docker daemon can look at the metadata associated with the containers and lists them.Docker does this in following steps:

1. The container ID.

2. The image from which it got created.

3. The command that was run after starting the container.

4. The details about when it got created.

5. The current status.

6. The ports that are exposed from the container and mapped from the machine.

7. The name of the container.


*Additional Info *

 To list both running and stopped containers, use the -a option as follows:-

Command : 

    ```
    $ docker ps -a
    ```


Output : 


  ```
  CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                           PORTS               NAMES
  fac59b475b1b        nginx               "/bin/bash"              2 minutes ago       Up 2 minutes                     80/tcp, 443/tcp     nginx      c18653529e7e        centos              "/bin/bash"              3 minutes ago       Up 3 minutes                                         cent
  8bee71c25e80        ubuntu              "/bin/bash -c 'while "   32 minutes ago      Exited (0) 32 minutes ago                            angry_noy
  f0394077c839        nginx               "nginx -g 'daemon off"   8 days ago          Exited (0) 8 days ago                                franklin
  1c526f8414fe        centos              "bash"                   8 days ago          Exited (127) 8 days ago                              web
  ```


To return just the container IDs of all the containers, use the -aq option as follows:

Command : 

     ```
     $ docker ps -aq
     ```

Output :

    ```
    fac59b475b1b
    c18653529e7e
    8bee71c25e80
    0944d42a91f2
    6a92c790a9f4
    aebca9261ce8
    ```

To show the last created container, including the non-running container, run the following command :

   ```
   $ docker ps -l
   ```

For filtering the containers on the basis of there use --filter/-f option with 'ps'.


##Looking at Logs of Containers: 


We can look at the log files of existing containers. To do so, let us first start a container running in an infinite loop.
 

Command :

    ```
    $ docker run -d centos /bin/bash -c "while [ 1 ]; do echo hello docker; sleep 1; done"
    ```

Output : 

       ```
       5317d59aaa505643a94b9d87e963e9090a047530b37e702f87c8d7c99b223905
       ```

    ```
    $ docker run 5317d59aaa505643a94b9d87e963e9090a047530b37e702f87c8d7c99b223905
  
    hello docker
    hello docker
    hello docker
    hello docker
    hello docker
    hello docker
    hello docker
    hello docker
    ``` 


Docker does this by looking at the container's specificlog file from /var/lib/docker/containers/<Container ID> and show the result.


*Additional Info*

With the -t option, we can get the timestamp with each log line and with -f we can get tail like behavior.




##Stopping a Container


We can stop one or more container at once. Firstly we'll create a container and then will stop it.


Note: Make sure that the Docker daemon is running on the host and you can connect through the Docker client.You will also need one or more running containers.


1.To stop the container, run the following command: 
      
   ```
   $ docker stop [-t|--time[=10]] CONTAINER [CONTAINER...]
   ```

2.If you already have a running container, then you can stop it; if not, first we will create a container and then stop it using followings steps:
      
     ```
     $ ID='docker run -d -i ubuntu /bin/bash'
    
     $ docker stop $ID 
     
          OR
      
     $ docker stop CONTAINER_ID
     ```

Note : This will save the state of the container and stop it. It can be started again, if needed.


*Additional Info *

1.To stop a container after waiting for some time, use --time/-t option.

2.To stop all the running containers , run the following command:
    
   ```
   $ docker stop 'docker ps -q'
   ```

## Deleting a Container


We can delete a container permanently, but before that we have to stop the container or use the force option. Here, we will start, stop and delete a Container.
 
*Note:* Make sure the Docker daemon is running on the host and you can connect to the docker client. You will also need some containers in a stopped or running state to delete them.

Steps to be followed: 

1.To delete the container, run the following commands: 

    ```
    $ docker rm [OPTIONS]] CONTIANER [CONTAINER..]
    ```

2.Let's first start a container, stop it and then delete it using the following commands:

   ```
   $ ID='docker run -d -i ubuntu /bin/bash'
   $ docker stop $ID
   $ docker rm $ID
   ```

OUPUT:

    ```
    $ ID1='docker run -di ubuntu /bin/bash'
    
    ef24b4805bf43550083184efcf6bfd7fd767cdaa86b0c45c8ed094df528d2c31
    ```
       

   ```
   $ docker stop $ID1
 
   ef24b4805bf43550083184efcf6bfd7fd767cdaa86b0c45c8ed094df528d2c31
   ```

   ```
   $ docker ps
   ```

  ```
  CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
  fac59b475b1b        nginx               "/bin/bash"         About an hour ago   Up About an hour    80/tcp, 443/tcp     nginx
  c18653529e7e        centos              "/bin/bash"         About an hour ago   Up About an hour                        cent
  6986b0367d5f        ubuntu              "/bin/bash"         2 hours ago         Up 2 hours                              nostalgic_jenni
  ```

   ```
   $ docker rm $ID1
   ```

*Additional Info*

To forcefully delete a container without an intermediate stop,use the -f option.

To delete all the containers, we first need to stop all the running containers and then remove them. Be careful before running the commands as these will delete both the running and stopped containers: 

  ```
  $ docker stop 'docker ps -q'
  ```
  
  ```
  $ docker rm 'docker ps -aq'
  ```


There are options to remove a specified link and volumes associated with the containers.


Note: The Docker daemon will remove the read/write layer, which was created while starting the container.


## Setting Restart Policy

With the Docker version 1.2.0 the *--restart*  flag is added with *docker run* tospecify the restart policy for the container. Three policies are defined with restart flag : 

    no : Do not restart the container if it dies (default)

    on-failure : Restart the container if it exits with a non-zero exit code

    always : Always restart the container no matter what exit


Syntax: 
      ```
      $ docker run --restart=POLICY [OPTIONS] IMAGE[:TAG] [COMMAND] [ARG..]
      ```

Example: 
      ```
      $ docker run  --restart=always -d -i -t ubuntu /bin/bash
      ```
          
      ```
      $ docker run --restart=on-failure:2 -d -i -t ubuntu /bin/bash
      ```


## Getting Privileged Access

Linux divides  the  privileges  traditionally associated  with  superuser into  distinct units, known as capabilities, which can be independently enabled and disabled.Capabilities are a  per-thread attribute. 
 Docker starts a container with minimum capabilities, by providing privileged access we are actually giving more capabilities to the container to perform opertions that are normally done by root.


*Syntax for privileged mode*

    ```
    $ docker run --privileged [OPTIONS] IMAGE[:TAG] [COMMAND] [ARG..]
    ```
 

Example :  
        ```
        $ docker run --privileged=false -i -t ubuntu /bin/bash
        ```
   

The above above command will give overhead privileges to the container.Most common of them all are : 

    ```
    --cap-add
    --cap-drop
    ```
 

Docker run also has a feature where you can adjust the capabilities that your container requires. This means you can remove capabilities your container does not need. For example, if your container does not need SETUID and SETGID you can remove this access by executing:


Syntax: 
    
    ```
    $ docker run --cap-drop  [OPTIONS] IMAGE[:TAG] [COMMANDS] [ARG..]
    ```

Example: 
 
    ```
    $ docker run --cap-drop setuid --cap-drop setgid -ti rhel7 /bin/sh
    ```

All capabilities can be added or removed. 
  
Syntax: 
    
    ```
    $ docker run --cap-add [OPTIONS] IMAGE[:TAG] [COMMANDS] [ARG..]
    ```

Example: 
   
    ```
    $ docker run --cap-add all --cap-drop sys-admin -ti rhel7 /bin/sh
    ```

This command will add all capabilities except sys-admin capability.


To remove chown capability fromt the container ,use following command : 
   
   ```
   $ docker run --cap-drop=CHOWN [OPTIONS] IMAGE[:TAG] [COMMAND] [ARG..]
   ```


## Exposing a Port


Port exposure can be done by many ways, one of them is through *run* command. Other ways are through Docker file and --link command.

Port exposed through run command : 

Syntax : 

     ```
     $ docker run --expose=PORT [OPTIONS] IMAGE[:TAG] [COMMAND] [ARG..]
     ```

Example : 
      
    ```  
    $ docker --expose=22 -i -t ubuntu /bin/bash
    ```
This command will expose port while starting the container. 

 

## Accessing Host Device Inside the Container


With the release of Docker version 1.2.0, host device can be accessed inside the container, by using --device option with the run command.
Before it has to be done with the --privileged option. 


Syntax : 
     
    ```   
    $ docker run --device=<Host device>:<Container Device Mapping>:<Permissions> [OPTIONS] IMAGE[:TAG] [COMMAND] [ARG..]
    ```


Example : 
       
    ```
    $ docker run --device=/dev/bdc:/dev/abdc -i -t ubuntu /bin/bash
    ```

This command will access the given folder inside the container.


## Injecting new process to a Running Container 

 
The docker *exec* command runs a new command in a running container.

The command started using docker exec only runs while the container’s primary process (PID 1) is running, and it is not restarted if the container is restarted.

If the container is paused, then the docker exec command will fail with an error.

Syntax : 
   
     ```
     $ docker exec [-d|--detach[=false]] [--help] [-i|--interactive[=false] [-t|--tty[=false] [CONTAINER] [COMMAND] [ARG..]
     ```


Example : 
        1. Run a new command inside the container . 
       
          ```
          $ docker run -i -t -d ubuntu /bin/bash
          ```

2. To attach a new command inside a running container,following syntax is used:

      ```
      $ docker exec -i -t [CONTAINER_ID] /bin/bash
      ```


## Inspecting

Docker *inspect* command is used for returning low-level information about a conatiner like container's configuration details etc.


Syntax : 
      ```
      $ docker inspect [-f|--format="" CONTAINER|IMAGE [CONTAINER|IMAGE...]
      ```

Example :
        ```
        $ Z='docker run -dit ubuntu /bin/bash'
        ```
        
        ```   
        $ docker inspect $Z
        ```


 ```
{
    "Id": "e90df13f8efb309eadf863933394c1b3d5ff015eb865629a72eed153be1621b6",
    "RepoTags": [
        "docker.io/docker:latest"
    ],
    "RepoDigests": [],
    "Parent": "f0ae8b630f082270bfe314ea44e3f188208d294ca86a66a302c01827a309ff16",
    "Comment": "",
    "Created": "2016-01-26T00:19:57.664906478Z",
    "Container": "f7e658063cdce5e24ccaf4b79b34befaab309d1d7711d6221350abc0ffa2fe10",
    "ContainerConfig": {
        "Hostname": "4ecf6d9f407a",
        "Domainname": "",
        "User": "",
        "AttachStdin": false,
        "AttachStdout": false,
        "AttachStderr": false,
  
 . . . . . . . .        
 . . . . . . . . 
}]
```

## Labeling and Filtering Containers 
 

Docker 1.6.0 has added a new feature called labeling, through which we can attach arbitrary key-value metadata. They can be treated as environment variable which are not available to running applications inside the container, but are available to programs ( Docker CLI ) that manages containers and images.
 Labels attached to images also gets applied to the containers started via them.


   ``` 
   $ docker images
   ```
 
   ```
   REPOSITORY                TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
   skhare/fedora             httpd               d33f23884c55        6 days ago          362.2 MB
   docker.io/docker          latest              e90df13f8efb        2 weeks ago         36.87 MB
   <none>                    <none>              0d189ff6a3e2        2 weeks ago         6.649 MB
   docker.io/ubuntu          latest              6cc0fc2a5ee3        2 weeks ago         187.9 MB
   docker.io/fedora          latest              3fc68076e184        5 weeks ago         206.3 MB
   docker.io/centos          latest              c8a648134623        6 weeks ago         196.6 MB
   ```

     
   ```
   $ docker images --filter label=distro=ubuntu
   ```


   ```
   REPOSITORY                TAG                 IMAGE ID            CREATED             VIRTUAL SIZE

   centos                   latest              c8a648134623        6 weeks ago           196.6 MB
   ```


Syntax for starting container with label-  --label/-l option :
  
   ```
   $ docker run --label environment=dev ubuntu date
   ```


Listing containers with labels specified will return only those containers which comes under that label, without label all the containers will be listed. 


   ``` 
   $ docker ps -a
   ```


   ``` 
   CONTAINER ID     IMAGE           COMMAND          CREATED           STATUS                      PORTS        NAMES
                 
   24f514c7ff48    ubuntu        "/bin/bash"      25 hours ago      Exited (127) 25 hours ago                kickass_lamarr
   57cda8093f0b    ubuntu       "/bin/bash"       25 hours ago      Exited (0) 25 hours ago                  admiring_kare
   3b7d8fcd0a1d    ubuntu       "/bin/bash"       30 hours ago      Up 30 hours                              romantic_jepsen
   0b545af689ce                  "/bin/bash       6 days ago        Exited (137) 3 days ago                  cranky_meninsky
   ```


    ```
    $ docker ps -a --filter label=environment=dev
    ```


    ```
    CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                  PORTS               NAMES
    b0c4c2b63188        ubuntu              "date"              7 days ago          Exited (0) 7 days ago                       ubt10
    ```
