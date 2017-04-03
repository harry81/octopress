---
layout: post
title: "Elasticbeans talk"
date: 2016-12-20 17:44:50 +0900
comments: true
categories: elasticbeanstalk
---


#### 헤더에 Authorization 값 전달이 기본 설정이 아니다.
request에 호함된 모든 헤더값(사용자 토큰값)이 서버에 자동으로 전달이 되는 것이 기본인줄 알았는데, 그렇지 않더라. 

``` bash [wsgi_enabled_pass.config]
container_commands:
  01_wsgipass:
    command: 'echo "WSGIPassAuthorization On" >> ../wsgi.conf'
```
http://mattharris.org/2015/11/30/setting-aws-elasticbeanstalk-environment-wsgi-authorization/
