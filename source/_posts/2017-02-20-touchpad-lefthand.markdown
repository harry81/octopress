---
layout: post
title: "touchpad는 오른손 잡이로, mouse는 왼손 잡이로 설정하기"
date: 2015-01-18 17:53:38 +0900
comments: true
categories: touchpad lefthand ubuntu
---


ubuntu에서 touchpad는 오른손 잡이로, mouse는 왼손 잡이로 설정하기

우분투 설정에서 mouse는 왼손 잡이로 설정
아래 명령어를 이용하여 마우스 버튼 배치만 변경

``` bash
xinput set-button-map 'Logitech USB Optical Mouse' 3 2 1
```
