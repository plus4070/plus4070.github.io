---
layout: post
title: InstanceOf? (feat.typeof)
categories: [NHN Entertainment DevDays]
tags: [instanceof, typeof, Javascript, Java]
comments: true
description: 아는 것 같지만 잘 모르는 instanceof
---

## InstanceOf? ##

`instanceof` 연산자는 프로그램 실행시 참조 데이터형을 검사하기 위해 사용되는 연산자이다. 쉽게 말하면 데이터 타입을 리턴해주는 함수라고 생각하면 된다.

<br>

### example(JAVA) ###

```java
String word = "hello";
System.out.println(word instanceof String);
```
`instanceof`의 왼쪽에는 데이터형을 검사할 변수나 값이 오게되고, 오른쪽에는 예상하는 데이터 형이 오게 된다. 실제 데이터 형과 예상하는 데이터 형이 같은 경우에는 `true`를 반환한다.

<br>
그러나 여기서 예외경우가 몇 가지 존재하는데, 그 중 하나는 `instanceof`를 사용하는 **왼쪽 변수가 오른쪽의 클래스와 상속관계가 없는 경우**이다. 아래의 경우를 보자.

```java
String word = "hello";
System.out.println(word instanceof Integer);
```

이 경우에는 에러가 발생하게 된다. Integer와 String은 상속관계에 있지 않기 때문이다.

<br>

또 다른 예외경우는 **객체가 아닌 기본 데이터형을 비교**하고자 할 때 발생한다.

```java
int num = 0;
System.out.println(a instanceof Integer);
```

<br><br>

### example(Javascript) ###

Javascript에는 `instanceof`뿐만 아니라 `typeof`라는 키워드가 존재한다. `typeof`키워드는 타입을 확인하고 해당하는 변수의 **Primitive 타입을 String으로 돌려준다.** 돌려주는 Primitive 타입은 아래와 같다.

- undefined
- boolean
- number
- string(이것은 primitive type으로 String과는 다르다)
- Object
- function

`instanceof`는 `typeof`와는 조금 다르다. Javascript의 `instanceof`는 Java에서의 `instanceof`와 비슷하다고 할 수 있다. 그렇지만 문제는 Javascript라는 언어에서 시작된다. Java와 Javascript는 엄연히 다른언어이다. 대표적인 예로 `{}`와 `[]`를 들 수 있다. `{}`는 `new Object();`와 같은 의미를 가지며, `[]`는 `new Array();`와 같은 의미를 가진다.

<br>

```javascript
"foo" instanceof String; // === false
"foo" instanceof Object; // === false
true instanceof Boolean; // === false
true instanceof Object; // === false

[0,1] instanceof Array; // === true
{0:1} instanceof Object; // === true

var color1 = new String("red");
var color2 = "red";
color1 == color2; // === true
color1 instanceof String; // === true
color2 instanceof String; // === ?
```

제일 아래의 경우는 `false`가 나온다. `color2`는 primitive type string으로 String과는 다르다. 하지만 이중등호로 비교해보면 `true`가 나온다.

<br>

```javascript
"foo".constructor instanceof String; // === false
"foo".constructor === String; // === true?!
```

이건 또 무슨상황인지? "foo"의 형은 String이 아니지만 삼중등호로 비교하면 String과 같다고 한다. 이것은 `typeof "foo".constructor`를 통해 확인할 수 있다.
