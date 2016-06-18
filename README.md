# Swarm Cluster on AWS with Docker 1.12

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
$ ansible all -m shell -a "sudo docker ps"
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

This play downloads the tarball of 1.12 and replaces the existing Docker.

```
$ ansible-playbook bootstrap.yml
```

After a short time, you should get back on your feet with Docker 1.12 on all your nodes

```
$ ansible all -m shell -a "sudo docker version"
```

## Create your Swarm

Initialize your Swarm head

```
$ ansible head -m shell -a "sudo docker swarm init"
```

Join the nodes in your Swarm cluster, note that we only run this on the _nodes_ defined in the inventory.

```
$ ansible nodes -m shell -a "docker swarm join <IP_HEAD>:2377"
```

Test it

```
$ ansible all -m shell -a "docker node list"
```

## Now use it
