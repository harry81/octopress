---
layout: post
title: "docker를 이용한 hadoop"
date: 2017-02-13 17:12:32 +0900
comments: true

categories: hadoop docker
---

### 동기
* 하둡의 병렬처리 특성을 확인하기 위해서는 2대 이상의 물리적인 컴퓨터가 필요하다
* 실제로 물리적인 컴퓨터를 마련하기 위해서는 비용이 필요하다
* docker를 이용하면 가상의 운영체제를 가볍게 실행할수 있다는 얘기를 들어왔고, hadoop를 테스트하기에 적당하다고 판단하여 시도함


### 하둡 docker 이미지를 받자
[Apache Hadoop 2.7.1 Docker image](https://github.com/sequenceiq/hadoop-docker)


### 하둡 실행하기
`
docker run -it -p 50070:50070 sequenceiq/hadoop-docker /etc/bootstrap.sh -bash
`

## 하둡 정보를 웹UI로 확인하기
**docker-machine** 명령어를 이용해 다커이미지를 실행하는 머신의 ip를 구한다
```
$ docker-machine env hoodpub

export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://192.168.99.100:2376"
export DOCKER_CERT_PATH="/home/harry/.docker/machine/machines/hoodpub"
export DOCKER_MACHINE_NAME="hoodpub"
# Run this command to configure your shell: #
# eval "$(docker-machine env hoodpub)" #

```
**DOCKER_HOST**가 가진 값이 가상머신의 ip이므로 이 값을 기억하자.

![Overview](/images/hadoop-overview.png)

[http://192.168.99.100:50070](http://192.168.99.100:50070)


