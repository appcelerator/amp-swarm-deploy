# Swarm Cluster on AWS with Docker 1.12

The main playbook creates a cluster of EC2 instances on AWS, makes sure Docker 1.12+ is installed, and initializes Swarm on it.

## Amazon resources

The key and infrastructure roles will create a ssh key, a security group and EC2 instances on AWS.
The docker and swarm roles will update Docker, start the service and initialize the Swarm.

```
$ ansible-playbook swarm.yml
```

The inventory will be updated with the list of EC2 instances, in a swarm_mgr and swarm_node group, as well as a parent swarm group.

```
$ ansible -i inventory swarm -b -m shell -a "docker version | grep Version"
 Version:      1.12.1
 Version:      1.12.1
 Version:      1.12.1
```
