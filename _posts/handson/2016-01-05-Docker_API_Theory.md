---
layout: article
title: "The Docker API"
categories: articles
modified: 
tags: []
comments: true
ads: false
---

t is the Docker API?
 As we are aware, docker follows a ***client-server model*** wherein the Docker client, interacts with the Docker daemon. The docker client is a command shell that takes user commands and 'passes them on' to the docker daemon for execution. But how does this interaction take place?
 
 There must obviously be some agreed upon messaging strategy agreed between these components just like you need to know a language before you can talk to people. This 'language' is the [API](https://en.wikipedia.org/wiki/Application_programming_interface).

The docker client communicates the users request to the docker daemon through what are known as ***API calls***, which basically translates to the client, taking the users command or function and 'calling' the equivalent server command or function, getting the response, formatting it, if needed and giving it to the user. 

The Docker API, as exposed by the docker daemon is primarily, a [REST API](https://en.wikipedia.org/wiki/Representational_state_transfer).  This basically means using the API usage is as simple as : 

    $ curl -X POST -H "Content-Type: application/json" http://localhost:2376/containers/create -d '{
         "Image":"docker.io/centos"
         }'

At this point, dont worry about how the above command works, just know that it will create a container with base image as docker.io/centos. Note that this is a minimal command, and a few more options will be required to create a functional container that can actually be used. 

Also, the API can also be used with the programming language of your choice through the [Remote API Client Libraries](https://docs.docker.com/engine/reference/api/remote_api_client_libraries/). 

##Docker Events
To gain a deeper understanding of how the Docker API works, we need to know about docker states and events.

According to the [docker documentation](https://docs.docker.com/engine/reference/api/docker_remote_api/). There are many states that a docker container goes through that can be accessed and controlled from the Remote API.

![Various states that the Docker Remote API can control](https://docs.docker.com/engine/reference/api/images/event_state.png "Event States")
<p align="center">
	Image Reference : https://docs.docker.com/engine/reference/api/images/event_state.png
</p>
Some container-related events are not affected by container state, so they are not included in this diagram. These events are:

 - **export** emitted by `docker export` 
 - **exec_create** emitted by `docker exec`  
 - **exec_start** emitted by `docker exec` after **exec_create**

Running `docker rmi` emits an **untag** event when removing an image name. The `rmi` command may also emit **delete** events when images are deleted by ID directly or by deleting the last tag referring to the image.

The API provides commands to control the various states of a docker container or image

##Docker Remote API

Again, we refer back to the [docker documentation](https://docs.docker.com/engine/reference/api/docker_remote_api/),  Docker’s Remote API uses an open schema model. In this model, unknown properties in incoming messages are ignored. Client applications need to take this behavior into account to ensure they do not break when talking to newer Docker daemons.

The API tends to be REST, but for some complex commands, like attach or pull, the HTTP connection is hijacked to transport STDOUT, STDIN, and STDERR.

By default the Docker daemon listens on unix:///var/run/docker.sock and the client must have root access to interact with the daemon. If a group named docker exists on your system, docker applies ownership of the socket to the group.

If the user wishes that the docker daemon listens to on a different location, such as on a http port, he needs to specifically restart the docker daemon and tell it to do so. We will discuss how to do this in the hands on.daemon and tell it to do so. We will discuss how to do this in the hands on.
