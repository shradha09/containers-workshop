---
layout: article
title: "Docker Compose"
categories: articles
modified: 
tags: []
comments: true
ads: false
---

{% include toc.html %}

#What is Docker Compose?

According to the [Docker Documentation](https://docs.docker.com/compose/) , compose is a tool for defining and running multi-container Docker applications. With Compose, you use a **Compose file** to configure your application’s services. Then, using a single command, you create and start all the services from your configuration.

This is really great and useful in development, testing and staging work flows, as well as [CI](https://en.wikipedia.org/wiki/Continuous_integration) (Continuous Integration) environments.  

Using Compose is basically a three-step process.

 1. Define your app’s environment with a Dockerfile so it can be
    reproduced anywhere with `docker compose` (preferred) or use existing containers.
 2. Define the services that make up your app in docker-compose.yml so
    they can be run together in an isolated environment.
 3. Lastly, run docker-compose up and Compose will start and run your
    entire app.
    
For much deeper understanding, please go through the Docker documentation itself, as linked above.

#Docker Compose Lab

##Setting Up the Lab

The first step in using docker compose, is to set it up in your machine. To do so, you need to run a command : 

###Command : 

    $ curl -L https://github.com/docker/compose/releases/download/1.5.2/docker-compose-`uname -s`-`uname -m` > /bin/docker-compose

###Output : 

      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   600    0   600    0     0    382      0 --:--:--  0:00:01 --:--:--   382
    100 7739k  100 7739k    0     0   232k      0  0:00:33  0:00:33 --:--:--  266k

Once the download is complete, you need to give execute permission to the binary, with the following command

    $ chmod +x /bin/docker-compose

Now, we can verify the installation, with the command

###Command : 

    $ docker-compose --version

###Output : 

    docker-compose version 1.5.2, build 7240ff3

You can read more about the installation process on the [docker compose installation documentation](https://docs.docker.com/compose/install/) page.

> Note : Command completion for docker compose is not inbuilt and
> required installation of a shell script, as documented on the
> [documentation](https://docs.docker.com/compose/completion/).

##A Sample Application

First lets try to run a sample application using a combination of a word press container alongside a mysql container.

To do so, first we pull the necessary containers and data. Create a project folder for your self and make it your working directory.

Now, let us compose a `$ vi docker-compose.yml` file, with the following content

    wordpress:
      image: wordpress:4.3
      links:
        - db:mysql
      ports:
        - 8080:80
    
    db:
      image: mariadb
      environment:
        MYSQL_ROOT_PASSWORD: example

Once this is done, you can start running it by simply running 

###Command : 

    $ docker-compose up

###Output : 

    Starting wordpress2_db_1
    Starting wordpress2_wordpress_1
    Attaching to wordpress2_db_1, wordpress2_wordpress_1
    db_1        | 2016-01-11 10:00:33 140380493813696 [Note] mysqld (mysqld 10.1.10-MariaDB-1~jessie) starting as process 1 ...
    ...
    wordpress_1 | [Mon Jan 11 10:00:38.663331 2016] [core:notice] [pid 1] AH00094: Command line: 'apache2 -D FOREGROUND'

Do make sure you are in the same folder as your `docker-compose.yml` file.

Now that this is one, ssh into the same machine from another terminal in order to test this, install links, a text based browser, and access the URL below to test this out.

###URL : 

    http://127.0.0.1:8080

You should be able to access the first page of a standard wordpress set-up.

