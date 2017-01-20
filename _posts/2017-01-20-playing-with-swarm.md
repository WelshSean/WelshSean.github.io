---
layout: post
title: Scaling out test application with a Swarm
---

# Starting Up The Cluster
Details on how to do this are in the [swarm docs](https://docs.docker.com/engine/swarm/)

Lets start a three node cluster using controller, p1 and p2

```
pi@controller:~ $ sudo docker swarm init --advertise-addr 192.168.0.17
Swarm initialized: current node (1r2eiifd3vzbw9smuognp9sqn) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join \
    --token SWMTKN-1-6cskwtqc4l1qfhww2rgwhy025m0z8vwjhrkmu04jaa62qy0783-2w3r5zrot4x8s48chm3rm6urg \
    192.168.0.17:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.

pi@p1:~ $ sudo docker swarm join     --token SWMTKN-1-6cskwtqc4l1qfhww2rgwhy025m0z8vwjhrkmu04jaa62qy0783-2w3r5zrot4x8s48chm3rm6urg     192.168.0.17:2377
This node joined a swarm as a worker.

pi@p2:~ $ sudo docker swarm join     --token SWMTKN-1-6cskwtqc4l1qfhww2rgwhy025m0z8vwjhrkmu04jaa62qy0783-2w3r5zrot4x8s48chm3rm6urg     192.168.0.17:2377
This node joined a swarm as a worker.
```

# Create a Service containing the test application
Lets create a service with one replica running the test app

```
pi@controller:~/piClustering/ContainerTestApp $ sudo docker service create --replicas 1 --name test-service  -e AWS_DEFAULT_REGION='eu-west-2' -e AWS_ACCESS_KEY_ID='KEY_ID' -e AWS_SECRET_ACCESS_KEY='Secret_Access_key' welshsean/rpi-testapp:latest
7m6kirneby19xc4lq729dmj89
pi@controller:~/piClustering/ContainerTestApp $ sudo docker service ps test-service
ID                         NAME            IMAGE                         NODE        DESIRED STATE  CURRENT STATE          ERROR
el53v4sol4rg2wy9d5atqn6lu  test-service.1  welshsean/rpi-testapp:latest  controller  Running        Running 4 seconds ago 
```

# Now lets scale up to five instances
```
pi@controller:~/piClustering/ContainerTestApp $ sudo docker service scale test-service=6
test-service scaled to 6
pi@controller:~/piClustering/ContainerTestApp $ sudo docker service ps test-service
ID                         NAME            IMAGE                         NODE        DESIRED STATE  CURRENT STATE                    ERROR
c65559qfhjrxpt8bz2rg09ec4  test-service.1  welshsean/rpi-testapp:latest  controller  Running        Running 10 seconds ago           
670ja4ke7fr6sadpy336jrk1v  test-service.2  welshsean/rpi-testapp:latest  p2          Running        Starting less than a second ago  
bw5akd7f1wkeaqrrwznottdwn  test-service.3  welshsean/rpi-testapp:latest  p1          Running        Starting less than a second ago  
bt3zjcey5gkrzcdk4wsafu5c5  test-service.4  welshsean/rpi-testapp:latest  controller  Running        Running less than a second ago   
cdvnbn5260prhj6khjs73p3wc  test-service.5  welshsean/rpi-testapp:latest  p2          Running        Starting less than a second ago  
7ryc3c7f85djlwad77prypzjl  test-service.6  welshsean/rpi-testapp:latest  p1          Running        Starting less than a second ago 
```

And we can see the messages for the seperate nodes coming in 

![example screen]({{ site.url }}/images/Screen Shot 2017-01-14 at 00.18.58.png)

# Simulate node failure

```
pi@controller:~/piClustering/ContainerTestApp $ clusterhat off p2
Turning off P2
pi@controller:~/piClustering/ContainerTestApp $ sudo docker service ps test-service
ID                         NAME            IMAGE                         NODE        DESIRED STATE  CURRENT STATE               ERROR
c65559qfhjrxpt8bz2rg09ec4  test-service.1  welshsean/rpi-testapp:latest  controller  Running        Running about a minute ago  
670ja4ke7fr6sadpy336jrk1v  test-service.2  welshsean/rpi-testapp:latest  p2          Running        Running about a minute ago  
bw5akd7f1wkeaqrrwznottdwn  test-service.3  welshsean/rpi-testapp:latest  p1          Running        Running about a minute ago  
bt3zjcey5gkrzcdk4wsafu5c5  test-service.4  welshsean/rpi-testapp:latest  controller  Running        Running about a minute ago  
cdvnbn5260prhj6khjs73p3wc  test-service.5  welshsean/rpi-testapp:latest  p2          Running        Running about a minute ago  
7ryc3c7f85djlwad77prypzjl  test-service.6  welshsean/rpi-testapp:latest  p1          Running        Running about a minute ago  
pi@controller:~/piClustering/ContainerTestApp $ sudo docker service ps test-service
ID                         NAME                IMAGE                         NODE        DESIRED STATE  CURRENT STATE               ERROR
c65559qfhjrxpt8bz2rg09ec4  test-service.1      welshsean/rpi-testapp:latest  controller  Running        Running 2 minutes ago       
8wup73pbwl5bxpd870pm6modz  test-service.2      welshsean/rpi-testapp:latest  controller  Running        Running 5 seconds ago       
670ja4ke7fr6sadpy336jrk1v   \_ test-service.2  welshsean/rpi-testapp:latest  p2          Shutdown       Running about a minute ago  
bw5akd7f1wkeaqrrwznottdwn  test-service.3      welshsean/rpi-testapp:latest  p1          Running        Running about a minute ago  
bt3zjcey5gkrzcdk4wsafu5c5  test-service.4      welshsean/rpi-testapp:latest  controller  Running        Running about a minute ago  
2m5mkvasvaui2v2rxe7ny7mij  test-service.5      welshsean/rpi-testapp:latest  p1          Running        Running 4 seconds ago       
cdvnbn5260prhj6khjs73p3wc   \_ test-service.5  welshsean/rpi-testapp:latest  p2          Shutdown       Running about a minute ago  
7ryc3c7f85djlwad77prypzjl  test-service.6      welshsean/rpi-testapp:latest  p1          Running        Running about a minute ago 
```

# Recover from failure

Bring node back up

```
pi@controller:~/piClustering/ContainerTestApp $ clusterhat on p2
Turning on P2
pi@controller:~/piClustering/ContainerTestApp $ sudo docker service ps test-service
ID                         NAME                IMAGE                         NODE        DESIRED STATE  CURRENT STATE          ERROR
c65559qfhjrxpt8bz2rg09ec4  test-service.1      welshsean/rpi-testapp:latest  controller  Running        Running 4 minutes ago  
8wup73pbwl5bxpd870pm6modz  test-service.2      welshsean/rpi-testapp:latest  controller  Running        Running 2 minutes ago  
670ja4ke7fr6sadpy336jrk1v   \_ test-service.2  welshsean/rpi-testapp:latest  p2          Shutdown       Running 4 minutes ago  
bw5akd7f1wkeaqrrwznottdwn  test-service.3      welshsean/rpi-testapp:latest  p1          Running        Running 4 minutes ago  
bt3zjcey5gkrzcdk4wsafu5c5  test-service.4      welshsean/rpi-testapp:latest  controller  Running        Running 4 minutes ago  
2m5mkvasvaui2v2rxe7ny7mij  test-service.5      welshsean/rpi-testapp:latest  p1          Running        Running 2 minutes ago  
cdvnbn5260prhj6khjs73p3wc   \_ test-service.5  welshsean/rpi-testapp:latest  p2          Shutdown       Running 4 minutes ago  
7ryc3c7f85djlwad77prypzjl  test-service.6      welshsean/rpi-testapp:latest  p1          Running        Running 4 minutes ago  
pi@controller:~/piClustering/ContainerTestApp $ sudo docker node list
ID                           HOSTNAME    STATUS  AVAILABILITY  MANAGER STATUS
06fv1pqkpdpz1yninnazgw14n    p1          Ready   Active        
1r2eiifd3vzbw9smuognp9sqn *  controller  Ready   Active        Leader
5tz1mzrpsfs9xork90mq6wyub    p2          Down    Active        
bzaal11uj78xpn0y4wgf68wqf    p1          Down    Active        
c6yjoy5wej8pcxitrhbvejdmo    p2          Ready   Active  
```

Its fairly simple and it works pretty well although on the face of it it seems fairly simplistic.