# Network enumeration hands-on session
Although this session contains information regarding how to do network enumeration in 
kubernetes, similar methods apply to "standard" networks.

## Prerequisites 
- kubectl
- minikube

## Test scenario
We already gained access to a target network/host. We want to find out
all the necessary information to continue with the attack. 

## Test application
We are going to deploy a basic wordpress app together with our custom prober container.
You can find a step-by-step guide on how to install the wordpress app [here](https://kubernetes.io/docs/tutorials/stateful-application/mysql-wordpress-persistent-volume/). 
You can also find the required yaml files in the current project.

Run `kubectl apply -k ./example_deployment` to deploy the wordpress project.

Run `kubectl apply -f ./prober` to deploy the probe container.

### Content of the prober container
This container is a custom container built by us around the famous kali linux distro.
It provides all the necessary tools to get started with enumeration. Feel free to use
another container you might find more complete/suitable for your scenario.

## Topics covered (or at least introduced)

This is a (not complete) overview of what we have discussed during
our meeting.

### Get a shell in the probe container
kubectl exec -it "prober-container-id" -- bash

## Get network interfaces and run nmap
`ifconfig` gets network interfaces. Then you can run nmap with the specified subnet/subnets.

`nmap -T5 $(hostname -i)/24` should work in most of the cases. Why? 

Expected output: 
````text
Starting Nmap 7.91 ( https://nmap.org ) at 2021-02-14 19:36 UTC
Nmap scan report for 172.17.0.1
Host is up (0.000021s latency).
Not shown: 998 closed ports
PORT     STATE SERVICE
22/tcp   open  ssh
8443/tcp open  https-alt
MAC Address: 02:42:E0:E3:E4:BA (Unknown)

Nmap scan report for 172-17-0-2.kube-dns.kube-system.svc.cluster.local (172.17.0.2)
Host is up (0.000019s latency).
Not shown: 997 closed ports
PORT     STATE SERVICE
53/tcp   open  domain
8080/tcp open  http-proxy
8181/tcp open  intermapper
MAC Address: 02:42:AC:11:00:02 (Unknown)

Nmap scan report for 172-17-0-3.wordpress.default.svc.cluster.local (172.17.0.3)
Host is up (0.000022s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE
80/tcp open  http
MAC Address: 02:42:AC:11:00:03 (Unknown)

Nmap scan report for 172-17-0-4.wordpress-mysql.default.svc.cluster.local (172.17.0.4)
Host is up (0.000017s latency).
Not shown: 999 closed ports
PORT     STATE SERVICE
3306/tcp open  mysql
MAC Address: 02:42:AC:11:00:04 (Unknown)

Nmap scan report for docker-deployment-67657767d8-6lqfw (172.17.0.5)
Host is up (0.000032s latency)__.
Not shown: 999 closed ports
PORT     STATE SERVICE
8081/tcp open  blackice-icecap

Nmap done: 256 IP addresses (5 hosts up) scanned in 15.15 seconds
````
### Questions
- Do you recognize some services?
- Can you see a pattern in the DNS names?
- What is running in coredns port 8080 and 8181? (hint: [see](https://coredns.io/plugins/))
- What is running @172.17.0.1? Can you ssh into it? 

