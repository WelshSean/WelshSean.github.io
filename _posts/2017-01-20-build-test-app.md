---
layout: post
title: Building a Test Application To Run UNder Docker
---

# Building a test app for Docker

One of the things that my better half bought me for Christmas was a Clusterhat - my intention was to play with Swarm, Kubernettes, etc for self education purposes. After going through hell and high water to find four Pi Zeros to plug into it I finally set it up - here it is in all its glory.


![Clusterhat]({{ site.url }}/images/clusterhat.jpeg)


I then realised that I'd want something to run in any containers that I spin up across test clusters and it would be nice if this would send me some messages to show that it was running. With that in mind I thought that a little Python app sending messages to Amazon SQS would be a nice easy way to do this.

## The Application
The app is very simple, it consists of an infinite loop with a 10 second sleep during each interation. During each iteration it will send a message to SQS (using boto3) posting the hostname of the thing running the app and a datetime value - simple :-)

## The Container
Now to containerise the app - I decided to initially do this on a normal intel based machine rather than on the PI in order to keep things simple and make sure that if it didnt work , it wasnt because of my exotic Hardware choices.....

I built a dockerfile in order to build my image - it was my first real go at doing stuff with Docker in anger (other than the usual - Hello World, run a shell thing :-) ) - it has to be said that it was pretty straightforwards to do so.

Starting from the debian:latest image I then installed python and pip and using this I installed AWSCLI and boto3. One point of interest is that I sent some environment variables (AWS_DEFAULT_REGION, AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY) to nonsense values,  the reason for this was not to embed credentials into source code, later on I'd pass them into the container as I instantiate it.

One last point is the last thing I do in the Dockerfile is to run the test application.

The image can then be built as follows

```
git clone https://github.com/WelshSean/piClustering
cd piClustering/ContainerTestApp
docker build -t welshsean/testapp:latest .
```

and then the container can be run using the command below

```
docker run -d -e AWS_DEFAULT_REGION='eu-west-2' -e AWS_ACCESS_KEY_ID='your key id' \
-e AWS_SECRET_ACCESS_KEY='your key' welshsean/testapp:latest
```

You need to modify this line to reflect your desired target region and the credentials for an IAM user that has read-write access to your SQS queue, which at present I've defaulted to being called heartbeat. The values following each -e will override the nonsense values that I previously supplied for them during the build - this will lead to three environment variables being set inside the container, which AWSCLI and/or boto3 use to access the AWS API.

## Running the App
Of course you can also just download the image from Docker Hub and run it :-)

![Run From Docker Hub]({{ site.url }}/images/Screen Shot 2017-01-08 at 22.37.52.png) 

and bingo you can see the messages in SQS

![Run From Docker Hub]({{ site.url }}/images/Screen Shot 2017-01-08 at 22.46.15.png)
 
## Next Steps
Next I'll get this running on a Pi and then do some learning around Container Orchestration :-)