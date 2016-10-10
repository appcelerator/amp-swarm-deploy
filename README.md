# Swarm Cluster on AWS with Docker 1.12

Based on the Docker 1.12rc ansible playbook from https://github.com/skippbox/ansible-swarm

The main playbook creates a cluster of EC2 instances on AWS, makes sure Docker 1.12+ is installed, and initializes Swarm on it.
Then it deploys AMP and starts the services.

It requires Ansible version 2.1+, please check on Ansible web site for the upgrade procedure appropriate for your system.

## Amazon resources

The key and infrastructure roles will create a ssh key, a security group and EC2 instances on AWS.
The docker and swarm roles will update Docker, start the service and initialize the Swarm.

Docker is prepared for production use, the devicemapper using a LVM thin pool.

## Prerequisites

A VPC and a subnet (see configuration keys in dist.yml).

## Configuration

the ```dist.yml``` file contains default values.
the playbook will look for a ```custom.yml``` file, any variable defined there will override the dist.yml file.

key | description | default value
--- | ----------- | -------------
aws_profile | AWS CLI Profile Name | default
aws_region | AWS Region | eu-west-1
aws_vpc_id | VPC Id |
aws_subnet_id | Subnet Id |
ssh_key | Name of The AWS Ssh Key  | swarm
swarm_security_group_name | Security Group Name | swarm
swarm_num_managers | Number of Swarm Manager Nodes | 1
swarm_num_workers | Number of Swarm Manager Workers | 1
swarm_instance_type | EC2 Instance Type | t2.medium
volume_size | Size Of The Docker Devicemapper Volume (GB) | 100
docker_repo_channel | Channel of the Docker repo (main | testing | experimental) | main
amp_base_directory | Base Directory For the Swarm Script | /go/src/github.com/appcelerator
amp_branch | Git Branch of The AMP repo | master

# AMI id
swarm_template: ami-7abd0209 # CentOS Linux 7 x86_64 HVM EBS 1602 eu-west-1
# OS Family of the AMI
image_os_distribution: CentOS


## Build a Swarm

### Full play

```
$ ansible-playbook swarm.yml
```

The inventory will be updated with the list of EC2 instances, in a swarm_mgr and swarm_node group, as well as a parent swarm group.

### Partial play

The plays can be limited with the use of tags.

 - key:           creates/get the AWS key
 - provisioning   creates the EC2 instances, the security group and updates the inventory
 - docker         updates the Docker engine, enable the service, initializes the Swarm
 - application    installs AMP

### Adhoc commands
```
$ ansible -i inventory swarm -b -m shell -a "docker version | grep Version"
 Version:      1.12.1
 Version:      1.12.1
 Version:      1.12.1
```

## Docker image

The ansible playbook can be run from a Docker container:

    $ docker run appcelerator/amp-swarm-deploy
