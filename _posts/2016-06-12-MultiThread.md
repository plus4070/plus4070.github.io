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

**TaskExecutor**라는 이름의 인터페이스를 사용, ThreadPoolTaskExecutor를 추상화하고 정의되어있는 `execute()`메소드를 사용해 멀티쓰레드로 작업을 처리합니다. 

`execute()`함수의 인자로는 Runnable 인터페이스를 구현한 구현체가 들어가게 됩니다.

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

<br><br>

##### 비동기 메소드 호출 #####

```java
for (int rowIndex = 1; rowIndex < totalRowSize; rowIndex++) {
    ...

    taskExecutor.execute(new ConvertToJsonAndInsertPerRowTask(templateParameter, excelSearch));

    ...
}	
```

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