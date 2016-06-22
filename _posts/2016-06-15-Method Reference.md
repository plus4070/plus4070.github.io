---
layout: post
title: Java8 Method Reference?
categories: [NHN Entertainment DevDays]
tags: [Java8, Method Reference]
comments: true
description: !
---

## Method reference ##

메소드 참조는 람다식을 쓰는경우, 무명함수를 정의하여 사용하는 경우도 존재하지만 실제 존재하는 메소드를 호출하는 경우도 있는데 이 때, 람다식 대신 메소드 참조를 사용할 수 있다.

아래와 같이 두 사람의 생일을 비교하는 static 메소드가 있다.

```java
@Data
public class Person {

	...
    LocalDate birthday;

	...
    public static int compareByAge(Person a, Person b) {
        return a.birthday.compareTo(b.birthday);
    }
}
```

사람의 생일을 비교해서 정렬하는 람다식은 아래와 같이 작성할 수 있다.

```java
Person[] people = ...;

Arrays.sort(people, (Person a, Person b) -> {
	return a.getBirthDay().compareTo(b.getBirthDay());
});
```

하지만 나이를 비교하는 `compareByAge()`메소드가 이미 구현되어있기 때문에 아래와 같이 사용할 수 있다.

```java
Person[] people = ...;

Arrays.sort(people, (a, b) -> {
	return Person.compareByAge(a, b);
});
```

그리고 위의 경우처럼 람다식을 사용하는 것이 아닌, 이미 구현한 메소드를 참조해서 사용할 수도 있다.

```java
Person[] people = ...;

Arrays.sort(peole, Person::compareByAge); //참조 대상이 static 메소드인 경우
```

<br>

##### Reference의 종류 #####

| 종류 | 예제 |
| --- | --- |
| 참조 대상이 static 메소드인 경우 | ContainingClass::staticMethodName |
| 참조 대상이 특정 객체의 instance 메소드인 경우 | containingObject::instanceMethodName |
| 참조 대상이 특정 타입의 instance 메소드인 경우 | ContainingType::methodName |
| 참조 대상이 생성자인 경우 | ClassName::new |
