# Swarm Cluster on AWS with Docker 1.12

Docker 1.12 contains some significant changes to how Swarm cluster are created, managed and used.
Quickly put, Swarm is now embedded in the Docker engine and provides some advanced networking features like load-balancing and virtual IPs for services.

This playbook is meant as a quick way to test Docker 1.12 RC2 for #dockercon. 

The main playbook creates a cluster of Ubuntu 15.10 machines on AWS.
Then you can bootstrap those machines to create a Swarm cluster with the new Docker 1.12

## Start the cluster

Check the content of the main play and run it.
It will create a SSH key pair and a swarm security group for you.

```
$ ansible-playbook swarm.yml
```

Check the inventory file and after a bit of time, you should have Docker running everywhere:

```
$ ansible swarm -m shell -a "sudo docker ps"
52.208.57.134 | SUCCESS | rc=0 >>
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES

52.51.96.89 | SUCCESS | rc=0 >>
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES

head | SUCCESS | rc=0 >>
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES

52.30.66.217 | SUCCESS | rc=0 >>
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

## Install Docker 1.12 on all your nodes 

This play downloads the tarball of 1.12 and replaces the existing Docker it is a bit hackish (to say the least).
Note that 1.12 is still in RC state.

```
$ ansible-playbook bootstrap.yml
```

After a short time, you should get back on your feet with Docker 1.12 on all your nodes

```
$ ansible swarm -b -m shell -a "docker version | grep Version"
 Version:      1.12.0-rc2
 Version:      1.12.0-rc2
 Version:      1.12.0-rc2
 Version:      1.12.0-rc2
 Version:      1.12.0-rc2
 Version:      1.12.0-rc2
 Version:      1.12.0-rc2
 Version:      1.12.0-rc2
```

## Create your Swarm

Initialize your Swarm head

```
$ ansible head -b -m shell -a "docker swarm init"
```

Join the nodes in your Swarm cluster, note that we only run this on the _nodes_ defined in the inventory.

```
$ ansible nodes -b -m shell -a "docker swarm join <PRIVATE_IP_HEAD>:2377"
```

Test it

```
$ ansible head -m shell -a "docker node list"
```

## Now use it
