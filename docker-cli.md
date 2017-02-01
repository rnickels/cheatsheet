Clean up Docker environment
========================
Removes unused Docker assets.

```
$ docker system prune
```

Remove dangling images
=======================
Dangling images are untagged images, that are the leaves of the images 
tree (not intermediary layers).
1. List IDs of dangling images to remove
2. Remove dangling images

```
$ docker rmi $(docker images -q -f “dangling=true”)
```

Remove dangling volumes
=======================
Dangling volumes are volumes not in use by any container. To remove them, 
combine two commands: first, list volume IDs for dangling volumes 
and then remove them.
1. List IDs of dangling volumes to remove
2. Remove dangling volumes

```
$ docker volume rm $(docker volume ls -q -f “dangling=true”)
```

Remove exited containers
=======================
1. List IDs of exited containers to remove
2. Remove containers

```
$ docker rm $(docker ps -q -f “status=exited”)
```

Get a shell into Docker host 
======================
Sometimes you want to connect to the Docker host. The ssh command is the 
default option, but this option may not be available, due to security settings, 
firewall rules or other undocumented issues.

Nsenter, by Jérôme Petazzoni, is a small and very useful tool for this use cases. 
The nsenter command allows you to enter into namespaces. I like to use the 
minimalistic (580 kB) walkerlee/nsenter Docker image.

You can use --pid=host to enter into Docker host namespaces.

```
# get a shell into docker host
$ docker run --rm -it --privileged --pid=host \
     walkerlee/nsenter -t 1 -m -u -i -n sh
```

Enter into ANY container
=======================
It’s also possible to enter into any container with nsenter and 
--pid=container:[id OR name]. But in most cases, it’s better to use 
the standard docker exec command. The main difference is that nsenter doesn’t 
enter the cgroups, and therefore evades resource limitations (which can be 
useful for debugging).

```
# get a shell into ‘redis’ container namespace 
$ docker run --rm -it --privileged --pid=container:redis \
     walkerlee/nsenter -t 1 -m -u -i -n sh
```

Watching containers lifecycle
============================
Sometimes you want to see containers being activated and exited when you run 
certain docker commands or try different restart policies. The watch command 
combined with docker ps can be pretty useful here. I found that the docker stats 
command (even with --format option) is not useful for this because it doesn’t 
allow you to see the same information as you can with the docker ps command.

Display a table with ‘ID Image Status’ for active containers and refresh it 
every 2 seconds

```
$ watch -n 2 ‘docker ps --format \
    “table {{.ID}}\t {{.Image}}\t {{.Status}}”’
```

Always restart container
========================
Restart the redis container with a restart policy of always so that if the 
container exits, Docker will restart it automatically.

```
$ docker run --restart=always redis
```

Restart container on failure
============================
Restart the redis container with a restart policy of on-failure and a maximum 
restart count of 10.

```
$ docker run --restart=on-failure:10 redis
```

Use the Docker host network stack
=================================
There are times when you might want to create a new container and connect it 
to an existing network stack. This might be the Docker host network or 
another container’s network. This is helpful when debugging and auditing 
network issues. The docker run --network/net option allows you to do this.

The new container will attach to the same network interfaces as the Docker host.

``` 
$ docker run --net=host … 
```

Use another container’s network stack
=====================================
The new container will attach to the same network 
interfaces as the other container. The target container can be specified by id or name.

```
$ docker run --net=container:<name|id> …
```

Attachable Overlay Network
==========================
Using Docker Engine running in swarm mode, you can create a multi-host overlay 
network on a manager node. When you create a new swarm service, you can attach it 
to the previously created overlay network.

Sometimes you need to attach a new Docker container (filled with different 
networking tools), to an existing overlay network, in order to inspect the network 
configuration or debug network issues. You can use the docker run command for this, 
eliminating the need to create a completely new “debug service”.

Docker 1.13 brings a new option to the docker network create command: attachable. 
The attachable option enables manual container attachment.

```
# create an attachable overlay network 
$ docker network create --driver overlay --attachable mynet 
# create net-tools container and attach it to mynet overlay network 
$ docker run -it --rm --net=mynet net-tools sh
```