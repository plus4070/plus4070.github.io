---
layout: post
title: Parallel Test?
categories: [NHN Entertainment DevDays]
tags: [Parallel, Parameterized, Test]
comments: true
description: 일이 많아? 그러면 동시에하면되지!
---

## Parallel JUnit Test ##

### Why Parallel? ###

브라우저를 일일이 띄워서 테스트를 진행하는 Selelnium Test는 테스트 속도가 느리고 테스트할 브라우저의 수와 테스트할 동작의 수가 늘어나는 만큼 전체 테스트 시간이 길어지기 때문에 이를 줄이고자 **Parallel test**를 사용하게 됨.

Parallel test를 하기 위해서는 우선 `Parameterized` 클래스를 상속받는 클래스를 하나 만들고, 클래스 내부에서 `RunnerScheduler` 인터페이스를 상속받는 내부 클래스를 작성해 Thread의 생명주기를 설정해준다.

```java
public class Parallelized extends Parameterized {

    private static class ThreadPoolScheduler implements RunnerScheduler {
        private ExecutorService executor;

        public ThreadPoolScheduler() {
            String threads = System.getProperty("junit.parallel.threads", "16");
            int numThreads = Integer.parseInt(threads);
            executor = Executors.newFixedThreadPool(numThreads);
        }

        public void finished() {									//모든 Thread가 끝날경우 호출되는 함수
            executor.shutdown();
            try {
                executor.awaitTermination(10, TimeUnit.MINUTES);
            } catch (InterruptedException exc) {
                throw new RuntimeException(exc);
            }
        }

        public void schedule(Runnable childStatement) {
            executor.submit(childStatement);
        }
    }

    public Parallelized(@SuppressWarnings("rawtypes") Class klass) throws Throwable {
        super(klass);
        setScheduler(new ThreadPoolScheduler());
    }
}
```

참고 : http://junit.org/junit4/javadoc/latest/org/junit/runners/model/RunnerScheduler.html

이렇게 작성한 `Parallelized`클래스를 이용하여 Thread기반 테스트를 진행한다. `ThreadLocal`클래스를 이용해 WebDriver를 등록해주고 테스트코드가 실행되는동안 Thread에 등록한 WebDriver를 가져다 쓸 수 있도록 한다.

<br>

##### Parameterized #####

Parameter로 서로 다른 Input을 넣어주고 이를 테스트할 수 있도록 해주는 runner 클래스. 같은 테스트를 값만 변경하여 반복해서 진행할 경우에 사용되는 클래스 이다. 아래와 같은 순서로 진행된다.

1. `@RunWith(Parameterized.class)`로 Parameterized클래스를 같은 Application Context를 가지는 오브젝트로 만들어준다.
2. `@Parameters` 애노테이션을 이용하여 데이터의 집합을 클래스에 넘겨주도록 한다.

```java
@Parameterized.Parameters
public static Collection primeNumbers() {
  return Arrays.asList(new Object[][] {
      { 2, true },
      { 6, false },
      { 19, true },
      { 22, false },
      { 23, true }
  });
}
```

3. `@Parameters`애노테이션을 붙인 데이터 집합의 열과 같은 타입을 가지는 생성자를 public으로 선언한다.
4. 데이터 집합의 각 행을 변수로 선언한다.

```java
public PrimeNumberCheckerTest(Integer inputNumber, Boolean expectedResult) {
  this.inputNumber = inputNumber;
  this.expectedResult = expectedResult;
}    
```

5. 생성한 변수를 이용하여 테스트를 진행한다.

참고 : http://junit.sourceforge.net/javadoc/org/junit/runners/Parameterized.html
