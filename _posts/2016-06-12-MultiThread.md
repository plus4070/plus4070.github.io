---
layout: post
title: MultiThread in Spring
categories: [NHN Entertainment DevDays]
tags: [JUnit, MultiThread Programming, Parallel]
comments: true
description: !
---

## Spring을 이용한 멀티쓰레드 프로그래밍 ##

멀티쓰레드로 특정 작업을 하기 위해서는 두 가지의 방법이 존재합니다.

1. Spring에서 제공하는 ThreadPoolTaskExecutor를 사용
2. @Async 애노테이션을 사용한 비동기 메소드 구현

<br><br>

### ThreadPoolTaskExecutor 사용 ###

**TaskExecutor**라는 이름의 인터페이스를 사용, Thread의 개수를 자동으로 조절하고 ThreadPoolTaskExecutor를 추상화하고 정의되어있는 `execute()`메소드를 사용해 멀티쓰레드로 작업을 처리합니다. 

`execute()`함수의 인자로는 Runnable 인터페이스를 구현한 구현체가 들어가게 됩니다.

> 1. If the number of threads is less than the corePoolSize, create a new Thread to run a new task.
> 2. If the number of threads is equal (or greater than) the corePoolSize, put the task into the queue.
> 3. If the queue is full, and the number of threads is less than the maxPoolSize, create a new thread to run tasks in.
> 4. If the queue is full, and the number of threads is greater than or equal to maxPoolSize, reject the task.

우선, Task를 수행하기 위해 corePoolSize보다 작으면 스레드를 생성한다. 스레드의 수가 corePoolSize와 같거나 크면 Task를 Queue에 넣는다. Queue가 꽉 차고, 스레드의 수가 maxPoolSize보다 작으면 스레드를 생성하고 Queue에 있는 Task를 처리한다. Queue도 꽉 차고 스레드의 수도 maxPoolSize에 도달하면 Task를 reject한다.

<br>

##### Bean 생성 #####

Bean 생성은 아래와 같이 ThreadPoolTaskExecutor를 받아 taskExecutor를 사용하는 서비스 클래스에 생성자로 DI 시켜줍니다. 옵션으로 들어가는 인자는 다음과 같습니다.

- corePoolSize : 기본 쓰레드 수
- maxPoolSize : 최대 쓰레드 수
- queueCapacity : 큐의 허용량
- awaitTerminationSeconds : 자식들이 모두 종료될때까지의 타임아웃 시간

```xml
<bean id="taskExecutor" class="org.springframework.scheduling.concurrent.ThreadPoolTaskExecutor">
    <property name="corePoolSize" value="#{common['core.pool.size']}" />
    <property name="maxPoolSize" value="#{common['max.pool.size']}" />
    <property name="queueCapacity" value="#{common['queue.capacity']}" />
    <property name="awaitTerminationSeconds" value="#{common['await.termination.seconds']}" />
</bean>

<bean id="excelService" class="com.nhnent.toastmail.master.excel.service.ExcelServiceImpl">
	<constructor-arg ref="taskExecutor" />
</bean>
```

이렇게 생성한 Bean 객체를 가지고 서비스 클래스에서 `execute()`를 호출해서 Queue에 작업을 할당할 수 있습니다.

<br>

##### Runnable 인터페이스 구현 #####

Runnable 인터페이스를 구현하고 병렬처리할 작업을 `run()`메소드에 오버라이드합니다. 병렬처리할 메소드에서 필요한 객체들은 구현한 클래스의 생성자를 통해서 의존성 주입을 해주는 방법으로 넘겨줄 수 있습니다.

```java

...
private List<Map<String, String>> templateParameterList;
private ExcelSearch excelSearch;

@Autowired
private ExcelParameterService excelParameterService;

@Autowired
private ExcelMapper excelMapper;


public ConvertToJsonAndInsertPerListTask(List<Map<String, String>> templateParameterList, ExcelSearch excelSearch) {
    this.templateParameterList = templateParameterList;
    this.excelSearch = excelSearch;
}

@Override
public void run() {
    List<ExcelParameter> excelParameterList = new ArrayList<ExcelParameter>();

     for(Map<String, String> templateParameter : templateParameterList) {
         ExcelParameter excelParameter = excelParameterService.makeExcelParameter(templateParameter, excelSearch);
         excelParameterList.add(excelParameter);
     }

     excelMapper.insertExcelParameterList(excelParameterList);
}
```

<br>

##### 비동기 메소드 호출 #####

```java
for (int rowIndex = 1; rowIndex < totalRowSize; rowIndex++) {
    ...

    taskExecutor.execute(new ConvertToJsonAndInsertPerRowTask(templateParameter, excelSearch));

    ...
}	
```
<br><br>

### @Async ###

Spring에서 제공하는 애노테이션인 `@Async`애노테이션을 사용하면 비동기 처리를 가능하게 해줍니다. 

메소드 상단에 `@Async`애노테이션을 붙이고 해당 메소드를 호출할 경우, Spring이 자체적으로 생성한 큐를 사용해 비동기 처리를 합니다.

<br>

##### @Async 사용 #####

```java
@Async
public void insertListAsJsonParameter(List<Map<String, String>> templateParameterList, ExcelSearch excelSearch) {
    List<ExcelParameter> excelParameterList = new ArrayList<ExcelParameter>();

     for(Map<String, String> templateParameter : templateParameterList) {
         ExcelParameter excelParameter = excelParameterService.makeExcelParameter(templateParameter, excelSearch);
         excelParameterList.add(excelParameter);
     }

     excelMapper.insertExcelParameterList(excelParameterList);
}
```

<br>

##### 비동기 메소드 호출 #####

```java
for(int i=0; i<CALL_TIMES; i++){
    excelAsyncService.insertListAsJsonParameter(templateParameterList, excelSearch); 
}
```

<br><br>

### Bounded Queue, Unbounded Queue

##### Unbounded queues (e.g. LinkedBlockingQueue without predefined capacity)

corePoolSize만큼의 모든 스레드가 바쁠 때, 태스크를 기다리게 한다.
새 스레드를 생성하지 않으므로 maximumPoolSize도 의미 없다.
웹서버처럼 각 태스크가 독립적인 경우 유용하다.
돌발적으로 갑자기 태스크가 급증하는 경우에 부드럽게 처리할 수 있는 장점이 있다.
하지만 큐에 대기하는 태스크가 매우 빠르게 증가할 수 있다.
* ThreadPoolExecutor는 사용하는 BlockingQueue를 생성할 때, 인자를 주지 않고 capacity를 정하지 않으므로 Unbounded queue처럼 동작한다.

##### Bounded queues (e.g. ArrayBlockingQueue)

CPU, OS, Context-Switching등의 리소스 소진(exhaustion)을 막는데 유용하다.
하지만 튜닝하기가 어렵다. 왜냐하면 큐 크기와 스레드풀 크기는 상호관계(trade off)가 있기 때문이다.
예를 들어, 큰 큐와 작은 풀을 사용할 경우, 리소스의 사용은 최소화할 수 있지만, 처리량(throuput)이 매우 작아진다.
예를 들어, 작은 큐와 큰 풀을 사용할 경우, CPU는 최대한 사용할 수 있지만, 많은 스레드에 의한 스케쥴링 오버헤드로 오히려 throuput 이 저하될 수 있다.
