---
layout: post
title: "CeleryTestSuiteRunner NoseTestSuiteRunner 함께 사용하기"
date: 2017-02-18 15:43:28 +0900
comments: true
categories: django testrunnser
---

소프트웨어 관리에 있어서 테스트는 언제나 필수적인 과정이지만, 번고롭다. 특히 매번 테스트를 실행할 때마다 db 스키마를 생성(CREATE)하고, 기존에 정의해둔 fixture를 입력(INSERT)하는 과정을 거친다.
테스트 환경을 일관되도록 유지를 시켜준다는 장점이 있지만, 시간이 걸린다는 단점이 있다.  

시간을 가지고 생각을 해보면, db scheme에 관련된 테스트 목적이 아니라면 때에 따라서 기존에 생성해둔 db를 그대로 이용하는 것도 똑똑한 선택일수 있다. 다행히 nose에서 이런 기능을 도와주는데, 방법은 환경변수 REUSE_DB의 값을 1로 설정하는 것이다.  

``` python 
# settings.py
TEST_RUNNER = 'django_nose.NoseTestSuiteRunner'
# 실행
REUSE_DB=1 python manage.py test --settings=main.settings_test -v 2 
```


하지만 celery 비동기 task를 테스트 하는중 문제를 발견했다. 비동기 작업을 테스트중 실시간 결과를 확인하는 것이 아니라, Queue에 작업 요청을 한후 그대로 테스트는 종료된다. 이렇게 되면 원했던 기능확인을 할수 없게된다. 다시 검색을 해 본결과 TEST_RUNNER를 **CeleryTestSuiteRunner**를 사용하라는 내용을 찾았다. 그 지시 그대로 따랐던 결과 **NoseTestSuiteRunner'**의 장점은 사라졌다.

`TEST_RUNNER = 'djcelery.contrib.test_runner.CeleryTestSuiteRunner'`

또 다시 고민하여 검색결과 아래와 같이 두 Test Runner의 기능을 모두 사용할수 있는 코드를 찾게되었다.  
[Link](http://stackoverflow.com/a/34282669/1118583)

``` python
from django_nose import NoseTestSuiteRunner
from djcelery.contrib.test_runner import CeleryTestSuiteRunner


class TestRunner(CeleryTestSuiteRunner, NoseTestSuiteRunner):
    pass
```

{% codeblock "settings.py" [line_number:(false)] %}
TEST_RUNNER = 'myapp.TestRunnser""
{% endcodeblock %}
