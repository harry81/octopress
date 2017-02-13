---
layout: post
title: "Celery beat를 Elasticbeanstalk에서도 사용하자"
date: 2017-02-12 17:35:05 +0900
comments: true
categories: celery beat elasticbeanstalk aws
---

* tip 1
{% codeblock lang:python title:"타스크를 모두 비우자" mark:3 %}
from celery.task.control import discard_all
    discard_all()
{% endcodeblock %}


* Elasticbeanstalk 에서 실행을 위한 명령어
{% codeblock lang:python title:"98-celery.config"  %}
command=/opt/python/run/venv/bin/celery beat -A main -S djcelery.schedulers.DatabaseScheduler --pidfile=
{% endcodeblock %}

**celery beat**를 실행하면 **celery worker**와 비슷한 task의 로그가 나타날 것으로 기대를 했다. 'Interval'과 'Crontab'으로 시간을 조정하며 결과를 확인해 보았으나 아래와 같은 기본적인 로그만이 나타났다

```
$ python manage.py celery beat -A main -S djcelery.schedulers.DatabaseScheduler --loglevel=DEBUG   
celery beat v3.1.17 (Cipater) is starting.
DEBUG 2017-02-13 16:19:33,691 base 29568 139948070696768 Configuring Raven for host: <raven.conf.remote.RemoteConfig object at 0x7f481f84af50>
__    -    ... __   -        _
Configuration ->
    . broker -> amqp://guest:**@localhost:5672//
    . loader -> celery.loaders.app.AppLoader
    . scheduler -> djcelery.schedulers.DatabaseScheduler

    . logfile -> [stderr]@%DEBUG
    . maxinterval -> now (0s)
[2017-02-13 16:19:33,880: DEBUG/MainProcess] DatabaseScheduler: intial read
[2017-02-13 16:19:33,880: INFO/MainProcess] Writing entries...
[2017-02-13 16:19:33,890: DEBUG/MainProcess] DatabaseScheduler: Fetching database schedule
[2017-02-13 16:19:33,902: DEBUG/MainProcess] Current schedule:
<ModelEntry: celery.backend_cleanup celery.backend_cleanup(*[], **{}) {4}>
<ModelEntry: core.tasks.scraper_example core.tasks.scraper_example(*[], **{}) {4}>
<ModelEntry: t1 core.tasks.test(*[], **{}) {4}>
[2017-02-13 16:19:33,931: INFO/MainProcess] DatabaseScheduler: Schedule changed.
[2017-02-13 16:19:33,931: INFO/MainProcess] Writing entries...
[2017-02-13 16:19:33,931: DEBUG/MainProcess] DatabaseScheduler: Fetching database schedule
[2017-02-13 16:19:33,936: DEBUG/MainProcess] Current schedule:
<ModelEntry: celery.backend_cleanup celery.backend_cleanup(*[], **{}) {4}>
<ModelEntry: core.tasks.scraper_example core.tasks.scraper_example(*[], **{}) {4}>
<ModelEntry: t1 core.tasks.test(*[], **{}) {4}>
[2017-02-13 16:19:33,952: INFO/MainProcess] Writing entries...

```
여러 시행착오를 거친결과 **beat**는 **worker**에게 정해진 시간만 알려주는 역할만 한다는 것을 알았다. 그리고 실제 작업은 **work**가 책임진다.

> 작업 확인을 위해서는 **beat**와 **worker** 동시에 실행을 하자.


```
python manage.py celery worker -A main --loglevel=DEBUG
```

```
python manage.py celery beat -A main -S djcelery.schedulers.DatabaseScheduler --loglevel=DEBUG
```
