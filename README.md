Testing the Source IP Address presented by a Docker Swarm
=========================================================

## Overview
This Vagrant file and associated directories is designed as a quick way to test the bug [25526](https://github.com/moby/moby/issues/25526)
Running `vagrant up` will result in 4 VM's:
* manager
* node01
* node02
* client

A three node Docker Swarm is initialised and a testresponder service and nginx service are started. there are 2 instances of the testresponder tornado app and a single instance of the nginx load balancer. The nginx service takes the source IP and adds it to a header on the inbound request, the Tornado app simply takes the inbound request and writes out all of the headers.
The aim here is to view the Source IP that teh Nginx service is presented with - in an ideal world this would be the IP address of the client node.

## Initial set up
The following steps could probably be handled in the Vagrantfile - but as this was a quick and dirty hack I haven't spent too long investigating this. therefore you will need to run the following command from the manager node to distribute your containers around the node:
`docker node update --availability drain manager`  
## Running the "Test"
From the client the following commands can be run and the response inspected:
* `curl 192.168.56.120:8080/testresponder`
* `curl 192.168.56.121:8080/testresponder`
* `curl 192.168.56.122:8080/testresponder`
The output will look something like:
* `Key: Host Value: 192.168.56.120:8080`
* **`Key: Sourceip Value: 10.255.0.2`**
* `Key: Connection Value: close`
* `Key: Accept Value: */*`
* `Key: User-Agent Value: curl/7.35.0`
Note that the sourceIP is **not** the client source IP.