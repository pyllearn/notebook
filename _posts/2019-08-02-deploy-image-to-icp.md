---
layout: post
title: "Deploy container image to ICP"
date: 2019-08-2 12:25:06 -0700
comments: true
---


How to deploy container image to ICP (IBM Cloud private)
============
To practice how to deploy existing internal projects to containerlize environment, here are some records to do that.

Here is the first POC to deploy a static website hosted on nginx web site.

#### Package the website
dockerfile
```
FROM nginx
COPY ./dist /usr/share/nginx/html
```
And then run the commands to build the image.

> docker build -t gssc-website .

Try to start an instance in local

> docker run --name testwebsite -p 83:80 -d gssc-website

#### Upload the image to icp
Add ICP host in local routing table.

> vim /etc/hosts

Add the line below
```
<%ip address%> vmwareregion312.icp
```
Login to icp registry using docker command
> docker login -u '<%xxxx@xx.xxx.com%>' -p '<%password%>' vmwareregion312.icp:8500 

Tag the image
>  docker tag  gssc-website vmwareregion312.icp:8500/uxl/gssc-website

Push the image to server
>  docker push vmwareregion312.icp:8500/uxl/gssc-website

#### Set the environment variable 
On ICP dashboard, go to User icon on the top right corner on ui, and click **Configure client**

<img src="./images/config.png">

Execute the commands in command line window.

Refer to [this](https://developer.ibm.com/tutorials/accessing-dockerhub-repos-in-iks/#access-dockerhub-repositories-from-the-ibm-cloud-kubernetes-service) tutorial to understancd how to configure remote repository for ICP
#### Run the image in icp environment
Start an instance of website
> kubectl run gssc-website --image=vmwareregion312.icp:8500/uxl/gssc-website --port=80 --env="DOMAIN=cluster"

Expose the port for user to access

> kubectl expose deployment gssc-website --type="LoadBalancer" 

<img src="./images/exposeService.png">

#### Verify the deployment by accessing host ip with the port
https://<%host ip%>:<%port%>

