# Installing Docker Swarm



1. [Verify the OS and Docker Version](#verify-the-os-and-docker-version)

2. [Create a Swarm](#create-a-swarm)

3. [Add Node to the Swarm](#add-node-to-the-swarm) 

4. [Docker Swarm Mode Ports](#docker-swarm-mode-ports)

5. [References](#references)


## Verify the OS and Docker Version

To check the OS version and it's architectuer and installed Docker version, enter the following commands:


```
$ cat /etc/os-release

$ dpkg --print-architecture

$ sudo docker --version
```

This tutorial uses two machines named `swarm-manager-01` and `swarm-worker-01`.

 
* The Swarm Manager Machine 

![installing-swarm-01](/docker-swarm-installation-images/installing-swarm-01-manager-os-version.jpg)



* The Swarm Worker Machine

![installing-swarm-01](/docker-swarm-installation-images/installing-swarm-02-worker-os-version.jpg)




## Create a Swarm


Run the following command to create a new swarm:


```
~$ sudo docker swarm init --advertise-addr <MANAGER-IP>

```

In the tutorial, the following command creates a swarm on the `swarm-manager-01` machine:


![installing-swarm-03](/docker-swarm-installation-images/installing-swarm-03-swarm-init.jpg)



The `--advertise-addr` flag configures the manager node to publish its address as 192.168.30.110. 
The other nodes in the swarm must be able to access the manager at the IP address.


The output includes the commands to join new nodes to the swarm. Nodes will join as managers or workers 
depending on the value for the `--token` flag.


Run the `docker node ls` command to view information about nodes.


```
~$ sudo docker node ls
[sudo] password for admin: 
ID                            HOSTNAME           STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
ny4vb6l4xa562gy26ypgmrq9b *   swarm-manager-01   Ready     Active         Leader           24.0.5
```

The `*` next to the node ID indicates that you're currently connected on this node.

Docker Engine swarm mode automatically names the node with the machine host name.


## Add Node to the Swarm


Once you've created a swarm with a manager node, you're ready to add worker nodes.

Open a terminal and ssh into the machine where you want to run a worker node. This tutorial uses
the name `swarm-worker-01`.

Run the command produced by the docker swarm init output from the [Create a Swarm](#create-a-swarm) step to create a worker node
joined to the existing swarm:

![installing-swarm-04](/docker-swarm-installation-images/installing-swarm-04-swarm-join.jpg)


If you don't have the command available, you can run the following command on a manager node to retrieve the join command
for a worker:

```
~$ sudo docker swarm join-token worker
[sudo] password for admin: 
To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-49p3voy0lkwnjhq5ymsevd1frtkbt33yikxyo9lsf4nr2mogn8-3o6o5xy20nt9sgiy37gbm3fge 192.168.30.110:2377
```


On the manager node runs and run the `docker node ls` command to see the worker node:


![installing-swarm-05](/docker-swarm-installation-images/installing-swarm-05-node-ls.jpg)


The `MANAGER STATUS` column identifies the manager nodes in the swarm. The empty status in this column for `worker1` identifies it
as a worker node.

Swarm management commands like `docker node ls` only work on manager nodes.


## Docker Swarm Mode Ports

The following ports must be available. On some systems, these ports are open by default.

* Port `2377` TCP for communication with and between manager nodes
* Port `7946` TCP/UDP for overlay network node discovery
* Port `4789` UDP (configurable) for overlay network traffic




Port `4789` is the default value for the Swarm data path port, also known as the **VXLAN** port. It is important
to prevent any untrusted traffic from reaching this port, as **VXLAN** does not provide authentication. This port
should only be opened to a trusted network, and never at a perimeter firewall.


Checking the used ports in the Swarm mode on the manager and worker machines:

* swarm-manager-01

![installing-swarm-06](/docker-swarm-installation-images/installing-swarm-06-manager-netstat.jpg)


* swarm-worker-01

![installing-swarm-07](/docker-swarm-installation-images/installing-swarm-07-worker-netstat.jpg)



## References

1. [Getting started with swarm mode](https://docs.docker.com/engine/swarm/swarm-tutorial/)

2. [Create a swarm](https://docs.docker.com/engine/swarm/swarm-tutorial/create-swarm/)

3. [Add nodes to the swarm](https://docs.docker.com/engine/swarm/swarm-tutorial/add-nodes/)

4. [Drain a node on the swarm](https://docs.docker.com/engine/swarm/swarm-tutorial/drain-node/)

