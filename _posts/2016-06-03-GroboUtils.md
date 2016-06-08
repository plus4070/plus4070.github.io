---
layout: post
title: MultiThread? 테스트 해보면되지 - GroboUtils
categories: [NHN Entertainment DevDays]
tags: [JUnit, GroboUtils, MultiThread Test, Test]
comments: true
description: !
---

## GroboUtils? ##

`GroboUtils`는 JUnit의 확장판 같은 느낌의 유틸성 클래스로, JUnit의 세 가지 주요 이슈를 보완하기 위해서 만들어진 오픈소스 프로젝트 입니다.

- Integration test
- Multi-threaded test
- Hierarchy test

`GroboUtils`에 관한 문서는 아래의 URL을 참고하시면 됩니다.

https://community.oracle.com/docs/DOC-982943

### GroboUtils 적용 ###

`GroboUtils`를 적용하기 위해서 아래의 URL에서 jar파일을 다운로드 받고 Java Build Path에 추가합니다.(Maven에 의존성 추가를 하려고했지만 missing artifact가 발생하길래 jar를 추가하는 방식으로 했습니다)

http://prdownloads.sourceforge.net/groboutils/GroboUtils-2-jars.zip


### GroboUtils 사용 ###

`GroboUtils`를 사용하기 위해서는 **TestRunnable**을 상속한 클래스를 사용하여 Runnable의 run 메소드의 동작을 정의하고, 해당 클래스를 **MultiThreadedTestRunner**에 등록하여 run하는 방식으로 사용할 수 있습니다. 각각의 스레드에 설정되어야 하는 값은 생성자를 통해 DI해줌으로써 전달할 수 있습니다.

##### TestRunnable 클래스 상속 ##### 

```java
public class Tester extends TestRunnable {

	int index;

	public Tester(int index) {
		this.index = index;
	}

    @Override
	public void runTest() { 
	    try {
            // 동작 정의
	    } catch (InterruptedException e) {
         	log.error(e.getMessage(), e);
	    }
    }
}
```

##### Multi-thread test구현 #####

```java
@Test
public void testM() throws Throwable {
    TestRunnable[] thread = new TestRunnable[THREAD_SIZE]; 

    for(int index = 0; index < THREAD_SIZE; index++) {
        thread[index] = new Tester(index); 
    }

    MultiThreadedTestRunner mttr = new MultiThreadedTestRunner(t);
        
    mttr.runTestRunnables();
}
```