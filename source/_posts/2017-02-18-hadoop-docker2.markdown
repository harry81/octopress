---
layout: post
title: "hadoop-docker"
date: 2017-02-18 17:13:49 +0900
comments: true
categories: 
---

가상머신 3개를 준비하자.
```
# Namenode 192.168.99.101
docker-machine start namenode; eval "$(docker-machine env namenode)";

# Datanode1 192.168.99.102
docker-machine start datanode1; eval "$(docker-machine env datanode1)";

# Datanode2 192.168.99.103
docker-machine start datanode2; eval "$(docker-machine env datanode2)";
```

```
...
namenode 192.168.99.101
datanode1 192.168.99.102
datanode2 192.168.99.103
```

각각의 가상머신에서 아래 명령어를 실행하여 필요한 hadoop 데몬을 실행하다.
```
# run hadoop-docker
docker run -it  -p 50070:50070 -p 8088:8088 sequenceiq/hadoop-docker /etc/bootstrap.sh -bash 
```
