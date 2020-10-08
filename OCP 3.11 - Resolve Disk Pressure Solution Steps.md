# OCP 3.11 - Resolve Disk Pressure Solution Steps

Contributors: Steele Desmond, Santosh Kumar Gurijala, Zarin Ohiba, Jon Woodlief
Last Edited: 10/5/2020

[Red Hat Recommended Solution](https://access.redhat.com/solutions/3396081)

First check if file system is full `df -i`

Then perform the following steps:

1. [Ensure garbage collection is enabled](https://docs.openshift.com/container-platform/3.11/admin_guide/garbage_collection.html)
2. [Ensure pruning is enabled](https://docs.openshift.com/container-platform/3.11/admin_guide/pruning_resources.html)
3. [Enable log rotation of docker and other logs](https://access.redhat.com/solutions/2334181)

## Garbage Collection

- OCP garbage collects terminated containers and images. Images are not garbage collected by default

- - Enable image collection for each node with disk pressure

```json
oc get cm -n openshift-node

kubeletArguments:
 image-gc-high-threshold:
	- "85" // Trigger at 85% capacity
 image-gc-low-threshold:
	- "80" // Attempt to prune until 80% capacity
```

## Pruning Objects

- [Prune groups, builds, deployments, and images from the etcd data store](https://docs.openshift.com/container-platform/3.11/admin_guide/pruning_resources.html)

- - Recommended to do once a day in some cases

- [Prune orphaned blobs](https://docs.openshift.com/container-platform/3.11/admin_guide/pruning_resources.html#hard-pruning-registry)

## Enable rotation of Docker and other logs

1. First check if log files are taking up significant space

```bash
find /var/lib/docker/ -name "*.log" -exec ls -sh {} \; | sort -n -r | head -20

du -aSh /var/lib/docker/ | sort -n -r | head -n 10
```

2. Login to OCP Registry and add *log-opt max-size* and *max-file*

```bash
cat /etc/sysconfig/docker

OPTIONS='--insecure-registry=172.30.0.0/16 --selinux-enabled --log-opt max-size=50m --log-opt max-file=5'

systemctl restart docker 
```

3. Remove existing logs on a container

```bash
cat /dev/null > $(docker inspect --format='{{.LogPath}}' CONTAINER_ID)
```
