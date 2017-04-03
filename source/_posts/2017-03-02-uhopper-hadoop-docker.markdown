---
layout: post
title: "uhopper-hadoop-docker"
date: 2017-03-02 17:11:16 +0900
comments: true
categories: 
---



### docker-compose 를 이용해서 ###

https://bitbucket.org/uhopper/hadoop-docker/


```
git clone https://bitbucket.org/uhopper/hadoop-docker.git 

docker network create hadoop
docker network create kafka
docker volume create --name=hadoop_datanode1
docker volume create --name=hadoop_datanode2
docker volume create --name=hadoop_historyserver
docker volume create --name=hadoop_namenode
docker-compose up

```



