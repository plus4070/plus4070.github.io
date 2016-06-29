---
layout: post
title: Java8 Stream API?
categories: [NHN Entertainment DevDays]
tags: [Java8, Stream API]
comments: true
description: !
---

## Java 8 stream ##

자바 8 스트림은 SQL에서 질의문을 사용하는 방법과 유사하게 구현을 할 수 있도록 만들어진 API입니다. 스트림 API를 사용하면 반복문을 복잡하게 작성하지 않아도 간단하게 리스트를 순회하면서 해당 값들에 대한 처리를 해줄 수 있습니다. 또한 스트림 API는 멀티코어를 지원하기 때문에 따로 멀티스레드 관련된 코드를 작성하지 않아도 된다고 합니다.

### Stream의 구성 ###

스트림은 여러 가지의 함수들로 구성되어있는데, 그 중 많이 쓰는 함수들 몇 가지에 대해서 정리하겠습니다.

<br>

##### filter() #####

`filter()`는 SQL문에서의 WHERE절과 유사하다고 생각하면 됩니다. 스트림을 받아서 `filter()`를 통해 접근할 데이터들에 속성을 명시해 줍니다. 쉽게 말해서 스트림에서 접근할 데이터의 조건이라고 생각하면 됩니다. 

아래의 예제는 list에서 짝수만을 골라 그 합을 계산해 리턴하는 스트림 구문입니다. 여기서 `filter()`안의 조건을 보면 스트림을 `number`라는 이름의 변수로 돌면서 그 값이 짝수인 경우만을 골라 그 합을 계산하는 것을 볼 수 있습니다.

```java
List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6);

Integer sumOfEven = list.stream()
        .filter(number->number.intValue() % 2 == 0)
        .mapToInt(Integer::intValue)
        .sum();

log.debug(sumOfEven.toString());	//12
```

내부의 조건은 데이터를 가공해서 선별하는 것이 아닌, 간단한 조건만을 가지기 때문에 위와 같이 inline으로 작성하였지만 만일, 선별 이전에 데이터에 어떠한 작업을 해야한다면 함수로 만들어 해당 함수를 호출하는 식으로 조건을 검사할 수 있습니다.

```java
Integer sumOfEven = list.stream()
        .filter(number->{
        	return number.intValue() % 2 == 0;
        })
        .mapToInt(Integer::intValue)
        .sum();
```

이런 `filter()`이외에도 조건문의 역할을 하는 메소드들이 존재합니다.

- distinct : 중복을 제거한 Element들을 리턴합니다.
- limit(n) : 주어진 사이즈(n) 까지의 스트림을 리턴합니다.
- skip(n) : 주어진 Element 길이 까지 제외한 나머지 스트림을 리턴합니다.

<br>

##### allMatch() #####

스트림으로 받아온 데이터들 중에 특정 조건에 맞는 데이터가 있는지 확인하는 메소드입니다. boolean값으로 리턴됩니다.

```java
boolean isNumberExist = numberList.stream()
	.allMatch(number -> number.intValue() > 30);
```

<br>

##### findAny(), findFirst() #####

주어진 스트림에서 임의의 요소를 찾는 메소드입니다. 값이 존재하거나 값이 없는 경우에 사용할 수 있는 컨테이너인 Optional을 리턴합니다. 만약 값이 존재한다면 특정 행동을 하도록 `ifPresent()`메소드를 사용할 수 있습니다.

```java
Optional<Integer> value = list.stream()
	.filter(number->number.intValue() > 3)
	.findAny();
```

<br>

##### map() #####

`map()`은 스트림에서 지정한 정보를 추출하기 위해 사용합니다. `filter()`에서와 마찬가지로 데이터를 가공해서 내보내려면 람다식으로 함수를 재정의해서 내보내면 됩니다. 아래의 예제는 필터링을 통해 걸리진 Element들을 `toString()`으로 데이터를 변환해서 스트림으로 반환하고 이를 `collect()`를 통해 List형태로 리턴하는 예제입니다.

```java
@Test
public void testString() {
    List<String> stringList = Arrays.asList("a", "ab", "c", "ad", "e", "f");

    List<String> aList = stringList.stream()
            .filter(value->value.contains("a"))
            .map(String::toString)
            .collect(Collectors.toList());

    log.debug(aList.toString());	//["a", "ab", "ad"]
}
```

<br>

##### collect() #####

`collect()`는 이전까지 스트림으로 받아서 가공하거나 필터링한 데이터를 Collections 형태로 내보내는 역할을 합니다. 이전에 호출했던 `filter()`나 `map()`등의 중간작업은 마지막 종료작업인 `collect()`가 호출되기 이전까지는 수행되지 않습니다. 마지막 `collect()`가 호출되었을 때 이전에 작업들을 병합하고 이를 리턴해줍니다.

<br>

##### reduce() #####

스트림을 순회하면서 각 Element들에 대한 반복적인 작업을 할 수 있는 메소드입니다. 인자는 리턴할 대상 변수의 초기값과, 동작을 정의한 함수를 받습니다.

```java
@Test
public void testReduce() {
    List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6);

    Integer value = list.stream()
        .reduce(0, (a, b) -> {
            return a+b;
        });
        
    log.debug(value);	//21, 만일 reduce의 초기값이 1인 경우에는 22 출력
}
```
