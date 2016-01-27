#Docker OpenStack Swift Object, Container, Account Server

This is a docker file that creates an OpenStack swift object, container, and account server image. 
At this point you must have already started a proxy server using following:
https://github.com/chalianwar/docker-swift-proxy


You can specify the object server workers and storage device at run time. Furthermore, you can
specify the ip address, path, and password of the machine from where you can scp
the ring files (you specified this machine when you ran conatiner for proxy server).


## startmain.sh

This Dockerfile uses supervisord to manage the processes.
Dockerfile we will be starting multiple services in the container, such as
object server, account server, container server, etc..


## Usage


```bash
[root@amaranth3 ~]# docker run -d -e SWIFT_OWORKERS=8 -e SWIFT_DEVICE=sdb1 -e SWIFT_SCP_COPY=root@192.168.0.171:~/files:kevin -p 6010:6010 -p 6011:6011 -p 6012:6012 --privileged -t alivt/swift-object
```

Over here, we mapped ports on host. In case you are launching multiple containers you need to choose different ports. Please note that you have to use the same ports that you mentioned when you launch proxy server as
ring files already contain this information.


Similarly, storage device that container
will use for storing the data is specified. The device will be mounted inside the container as we are launching
container in priviliged mode. Please, be sure you specify
the correct device which has enough disk space. Using incorrect device can be catastrophic.


SWIFT_OWORKERS is used to set the object workers dynamically.

The ring files created at the proxy server needs to be copied to the object servers as well. SWIFT_SCP_COPY
contains the remote location path from where ring files can be copied. root@192.168.0.171:~/files is the remote path, whereas kevin is the `scp password`.

At this point OpenStack Swift proxy is running.


```bash
hulk0@host1:~$ docker ps
CONTAINER ID        IMAGE                                     COMMAND                CREATED             STATUS              PORTS                     NAMES
54810e6f88e8        alivt/swift-object   "/bin/sh -c /usr/loc   13 minutes ago      Up 13 minutes       0.0.0.0:6010-6012->6010-6012/tcp   jovial_tesla
```

We need to launch as many containers as we specified at the time of launching proxy server. Once you are done with
that you can go back to the host running proxy server and start using object store to store and retrieve files/objects.


That's it!
