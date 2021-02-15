# Filesystem enumeration notes

## Prerequisites 
- kubectl
- minikube

## Test scenario
We already gained access to a target and we need to find out as
many information as possible in the filesystem

## Test application
We are going to deploy a basic wordpress app. You can find a step-by-step
 guide [here](https://kubernetes.io/docs/tutorials/stateful-application/mysql-wordpress-persistent-volume/) 
You can also find the required yaml files in the current project.

## Topics covered (or at least introduced)

This is a (not complete) overview of what we have discussed during
our meeting.

### Get a shell in the target container
kubectl exec -it <wordpress container id> -- bash

### Get environment variables
Use the `env` command. What we discovered:

    - Application secrets (e.g., login credentials)
    - Kubernetes specific variables
        - Hostname
        - Service ports 

### Enumerate files and binaries

We used [linpeas](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS)
to find information about the filesystem. 

### Find kubernetes specific information
e.g., look into: `/run/secrets/kubernetes.io`

### Docker socket attached to a deployment
We showed that attaching the docker socket as volume in
a kubernetes deployment can lead to unexpected result. We were able
to manage docker runtime and we created a sample container from
our deployment. 

A sample deployment file can be found [here](./docker_in_docker_example/deployment.yaml).
We run tests by using the [dockerode](https://github.com/apocas/dockerode) library
that comes installed as a dependency in the machine.

Snippet used: 

````js
var Docker = require('dockerode');
var docker = new Docker({socketPath: '/var/run/docker.sock'});
// List and print containers
docker.listContainers(function (err, containers) {
  containers.forEach(function (containerInfo) {
    console.log(containerInfo)
  });
});
// Run container
docker.run('ubuntu', ['bash', '-c', 'uname -a'], process.stdout, function (err, data, container) {
  console.log(data.StatusCode);
});
````