---
layout: article
title: "Micro OSes : CoreOS"
categories: articles
modified: 
tags: []
comments: true
ads: false
---

```
$ git clone https://github.com/coreos/coreos-vagrant.git
$ cd coreos-vagrant
$ cp user-data.sample user-data
```

- Create the new discovery token and update the config file

- Update the config file

```
$ cp config.rb.sample config.rb
```

- Start the VMs

```
$ vagrant up
$ vagrant status
```

- Login to one VM

```
$ vagrant ssh core-01
$ systemctl status etcd fleet
$ core@core-01 ~ $ fleetctl list-machines
MACHINE		IP		METADATA
bff3ffd4...	172.17.8.102	-
cf4afec9...	172.17.8.101	-
```

- Create a service file with following content

```
$ cat myapp.service
[Unit]
Description=MyApp
After=docker.service
Requires=docker.service

[Service]
TimeoutStartSec=0
ExecStartPre=-/usr/bin/docker kill busybox1
ExecStartPre=-/usr/bin/docker rm busybox1
ExecStartPre=/usr/bin/docker pull busybox
ExecStart=/usr/bin/docker run --name busybox1 busybox /bin/sh -c "while true; do echo Hello World; sleep 1; done"
ExecStop=/usr/bin/docker stop busybox1
```

- Create the service

```
$ fleetctl submit myapp.service
$ fleetctl start myapp.service
core@core-01 ~ $ fleetctl list-units
UNIT		MACHINE				ACTIVE	SUB
myapp.service	bff3ffd4.../172.17.8.102	active	running
```
