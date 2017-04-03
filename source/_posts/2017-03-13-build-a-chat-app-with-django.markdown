---
layout: post
title: "build a chat app with django"
date: 2017-03-13 14:29:24 +0900
comments: true
categories: 
published: true
---


기능 요구사항
-------------
  * 1:1 대화를 주고 받는 웹 어플리케이션을 작성해주시길 바랍니다.
  http://rocketchat.healworld.co.kr/
  
  * 화면은 이미지를 참고로 하여 작성해주시길 바랍니다. (화면의 모든 기능이 꼭 존재할 필요는 없습니다.)  
링크는 깨짐(404) 대략적인 위치는 기억하여 진행함(좌측 대화방, 오른쪽 대화 목록)
https://dl.dropboxusercontent.com/u/4338652/mini_project.png
  * 대화 상대별로 대화방이 나뉘어 있으며, 각 대화방끼리 이동할 수 있어야 합니다.  
  **구현 성공**
  
  * 메시지를 전송하고 서로 이 내용을 확인할 수 있어야 합니다.  
  **구현 성공**
  
  * 대화는 서버에 저장되어 언제든 확인할 수 있어야 합니다.  
  **구현 성공**  
  http://rocketchat-backend.healworld.co.kr/admin/   
  user:[rocket], password:[chat]
  * Django 또는 Flask로 작성해 주시길 바랍니다.  
  **Django==1.10.6**
  
  * Github, Bitbucket 등 접속 가능한 Git Remote Repository로 전달 바랍니다.
    Backend : https://github.com/harry81/rocketchat  
    Frontend : https://github.com/harry81/rocketchat-web  
    구조도 : https://goo.gl/7k5LUD

-------------------------------------------------------------------------------

진행
--------
  * 2017-3-20
    * ws4redis에서는 [users, groups, sessions, broadcast] 와 같은 대상으로만 메시지를 보낼 수 있다. 이번 구현의 목적에 맞게 대화방을 바탕으로 사용자가 가입/발행(subscribe/publish) 할 수 있도록 구현
    * 작업 결과 마무리 및 문서화

  * 2017-3-19
    * 사용자가 대화방을 변경한 경우 그에 해당하는 대화 목록이 노출되도록 변경
    
  * 2017-3-18
    * 그룹별 메시지 전송하기
    * 그룹별 메시지 보여주기

  * 2017-3-17
    * EC2 인스턴스 설정 완료(Nginx + uWsgi)
    * Frontend, Backend 구성 완료
    * Deploy script 준비 완료
    * APIs
```
/api-message/   chat.views.MessageAPIView       api-message-list
/api-message/<pk>/      chat.views.MessageAPIView       api-message-detail
/api-room/      chat.views.RoomAPIView  api-room-list
/api-room/<pk>/ chat.views.RoomAPIView  api-room-detail
```
  * 2017-3-16 AWS에서 소켓 사용의 어려움2  
AWS 에서 socket server 설정이 생각보다 알아야 할 제품 관련 지식이 많다. 즉 언어와 프레임웍 관련 내용이라기 보다는 AWS에서 제공하는 제품군의 사용과 설정에 대한 내용들이다. 아마존에서 web socket를 지원하나 이 기능을 위해서는 Classic Load Balancer 대신 Amazon Load Balancer으로 설정이 필요하다.
`Amazon Load Balancer ---- Target Group ---- Instance`  
생각보다 금방 마무리 될 줄 알았던 socket server 설정작업이 파면팔수록 걸리는 문제들이 많았으나, 이 방법밖에는 떠오르지 않아 3일 정도의 시간을 투자했다. 결국은 AWS 생태계를 더 헤아리지 않고서는 어렵다고 판단했다. EB 대신 별도의 인스턴스를 만들어 Nginx와 uWsgi로 설정하기로 결정  
    방향 전환 -> Elasticbeanstalk with Amazon Load Balancer ---> *EC2 with Nginx and uWsgi*
    
  * 2017-3-15 AWS에서 소켓 사용의 어려움
    * ELB 대신 ALB 사용 필요 
      * 이유 - http://stackoverflow.com/a/39491033/1118583
      * 설치 - https://blog.mangoforbreakfast.com/2017/02/13/django-channels-on-aws-elastic-beanstalk-using-an-alb/

  * 2017-3-14 실행 환경 세팅 및 브라우져에서 비동기 테스트
    * Elasticbeanstalk, RDS, Route 53, ELB
      * shell plus에서 redis queue에 아래와 같은 형식으로 메시지 통신 확인
``` python
    from ws4redis.publisher import RedisPublisher
    from ws4redis.redis_store import RedisMessage
    ...
    
    message = RedisMessage(json.dumps(msg))
    redis_publisher.publish_message(message)
```
    
  * 2017-3-13 django에서 chat을 하기 위한 기술 및 패키지 확인
    * https://github.com/stephenmcd/django-socketio
      github star 4개라서 기쁜 마음으로 테스트 해 보았으나, 패키지간 버젼 의존성 문제로 일단 다른 패키지 물색
    * https://github.com/jrief/django-websocket-redis
    * https://channels.readthedocs.io/en/stable/
    
-------------------------------------------------------------------------------

Packages
------
  * http://django-websocket-redis.readthedocs.io/en/latest/
  * https://github.com/stephenmcd/django-socketio
  * https://github.com/jrief/django-websocket-redis/tree/master/ws4redis
  * https://github.com/ottoyiu/django-cors-headers
  
  * https://github.com/angular/angular
  * https://github.com/afrad/angular2-websocket

-------------------------------------------------------------------------------

오류 및 해결
------------

### django-scoketio 설치후 ###

#### 문제 ####

```  python [linenos:false]
#Django==1.10
#django-socketio==0.3.9

  File "/home/harry/.virt_env/rocketpunch/local/lib/python2.7/site-packages/django_socketio/urls.py", line 1, in <module>
    from django.conf.urls import patterns, url
ImportError: cannot import name patterns
```

#### 해결 ####

``` python requirements.txt
-e git+https://github.com/stephenmcd/django-socketio.git#egg=django-socketio
```

#### 문제 ####
로컬에서 ws4redis를 이용하여 메시지를 주고 받는 것에는 문제가 없었으나 AWS상에서는 아래와 같은 소켓 통신에 문제가 발생

``` text
WebSocket connection to 'ws://rocketpunch-app.tdirjp8mny.ap-northeast-2.elasticbeanstalk.com/ws/chat?subscribe-broadcast&publish-broadcast' failed:
Error during WebSocket handshake: Unexpected response code: 404
...
app.component.ts:41 Error: undefined
```

``` text
angular2-websocket.js:52 WebSocket connection to 'ws://rocketpunch-app.tdirjp8mny.ap-northeast-2.elasticbeanstalk.com/ws/chat?subscribe-broadcast&publish-broadcast' failed:
Error during WebSocket handshake: Unexpected response code: 504
...
app.component.ts:41 Error: undefined
```

#### 해결 ####
  * Amazon Load Balance with Elastic Beanstalk
  https://blog.mangoforbreakfast.com/2017/02/13/django-channels-on-aws-elastic-beanstalk-using-an-alb/
  

#### 문제 ####
```
you need to build uWSGI with SSL support to use the websocket handshake api function !!!
Other Exception: unable to complete websocket handshake
Traceback (most recent call last):
  File "/home/ubuntu/virtualenvs/app/local/lib/python2.7/site-packages/ws4redis/wsgi_server.py", line 101, in __call__
    websocket = self.upgrade_websocket(environ, start_response)
  File "/home/ubuntu/virtualenvs/app/local/lib/python2.7/site-packages/ws4redis/uwsgi_runserver.py", line 52, in upgrade_websocket
    uwsgi.websocket_handshake(environ['HTTP_SEC_WEBSOCKET_KEY'], environ.get('HTTP_ORIGIN', ''))
IOError: unable to complete websocket handshake
Starting late response on websocket
Finish non-websocket response with status code: 500
```
#### 해결 ####
Recompile uwsgi with support openssl

#### 문제 ####
사용자가 request를 서버에 요청했을 때,
``` text nginx error log
2017/03/17 03:29:44 [error] 17644#17644: *2 upstream prematurely closed connection while reading response header from upstream, client: 172.31.4.186, server: rocketchat.healworld.co.kr, request: "GET / HTTP/1.1", upstream: "uwsgi://unix:///tmp/uwsgi.sock:", host: "rocketchatalb.healworld.co.kr"
2017/03/17 03:29:45 [error] 17644#17644: *2 upstream prematurely closed connection while reading response header from upstream, client: 172.31.4.186, server: rocketchat.healworld.co.kr, request: "GET /favicon.ico HTTP/1.1", upstream: "uwsgi://unix:///tmp/uwsgi.sock:", host: "rocketchatalb.healworld.co.kr", referrer: "http://rocketchatalb.healworld.co.kr/"

```

#### 문제 ####
local에서는 문제없이 시작 페이지가 열리나, `ng build` 이후 서버에서는 아래와 같은 오류발생하며 angular가 아무런 내용도 보여주지 않는다.

```
Uncaught (in promise): Error: DI Error / Unhandled Promise rejection: No provider for t! ; Zone: angular ; Task: Promise.then
```

#### 해결 ####

https://github.com/salemdar/angular2-cookie/issues/37




#### 해결 ####
> Installing the python plugin for uwsgi with apt-get install uwsgi-plugin-python and adding plugins = python to the individual uwsgi app config solved this problem for me on Ubuntu 11.10 when using upstart.

http://stackoverflow.com/questions/10748108/nginx-uwsgi-unavailable-modifier-requested-0

-------------------------------------------------------------------------------

설정
----

```text /etc/nginx/sites-available/rocketchat
upstream django {
        server unix:///tmp/uwsgi.sock;
    }

upstream ws {
        server unix:///tmp/ws.sock;
    }
server {
        listen 80;

        server_name rocketchat.healworld.co.kr rocketchatalb.healworld.co.kr ;
        index /;

        location / {
                include uwsgi_params;
                uwsgi_pass django;

        }

        location /ws/ {
                proxy_pass http://unix:/tmp/ws.sock;
                proxy_http_version 1.1;
                proxy_set_header Upgrade $http_upgrade;
                proxy_set_header Connection "upgrade";
        }
}
```
`sudo service  nginx restart`

``` text uwsgi config for django app
[uwsgi]
chdir           = /home/ubuntu/app
module          = main.wsgi
home            = /home/ubuntu/virtualenvs/app/

master          = true
processes       = 2
socket          = /tmp/uwsgi.sock
chmod-socket    = 666
vacuum          = true
```
`uwsgi --ini uwsgi.ini`

``` text uwsgi config for socket
[uwsgi]
chdir           = /home/ubuntu/app
module          = main.wsgi_websocket
home            = /home/ubuntu/virtualenvs/app/

master          = true
processes       = 100
http-socket          = /tmp/ws.sock
chmod-socket    = 666
vacuum          = true
```
`uwsgi --ini ws.ini`


#### static web page ####
``` text
# 버킷 생성
aws s3 mb  s3://rocketchat.healworld.co.kr/  --profile pointer --region ap-northeast-2
# 버킷을 website 가능하게 변경
aws s3 website s3://rocketchat.healworld.co.kr/ --index-document index.html --error-document error.html --profile pointer --region ap-northeast-2
# static files 업로드
aws s3 sync dist s3://rocketchat.healworld.co.kr/ --grants read=uri=http://acs.amazonaws.com/groups/global/AllUsers --profile pointer --region ap-northeast-2
```

-------------------------------------------------------------------------------

배포
----

```
fab deploy
```
