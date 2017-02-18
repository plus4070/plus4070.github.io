---
layout: post
title: Reflection?
categories: [NHN Entertainment DevDays]
tags: [Java, Reflection]
comments: true
description: !
---

## Reflection? ##

`Reflection`은 자바의 코드 자체를 추상화해서 접근하도록 만든 것 입니다.

```java
String name = "name";
```

위와 같은 코드가 있을 때, name이라는 String 객체의 길이를 알고 싶은 경우에 일반적으로는 `name.length()`를 호출하면 됩니다. 그렇지만 만약 String 클래스의 `length()` 함수에 대한 정보를 가져오고 싶다고 하면, Reflection을 이용하여 해당 메소드의 참조를 얻어올 수 있습니다.

```java
Method lengthMethod = String.class.getMethod("length");
```

참조해오는 메소드를 `Method` 타입으로 받아, 참조를 가져온 메소드의 정보를 확인하거나 조작할 수 있습니다. 위에서는 String클래스의 메소드들 중, length라는 이름을 가지는 메소드의 참조를 받아오는 방법입니다. 이를 실행시키기 위해서는 Method에 정의되어있는 `invoke()` 메소드를 통해서 실행할 수 있습니다.

```java
int length = lengthMethod.invoke(name); // name.length();
```

자바의 모든 클래스는 그 클래스 자체의 구성정보를 담고있는 `Class` 타입의 오브젝트를 가지고 있습니다. **클래스이름.class** 와 같은 방법으로 해당 객체를 확인할 수 있고, **getClass()** 함수를 호출해서 클래스 타입의 오브젝트를 반환받아 확인할 수 있습니다.

반환받은 클래스 오브젝트에는 해당 클래스의 메타정보를 가지고있는데요, 이 정보를 이용해 구현된 메소드는 무엇인지, 메소드의 파라미터와 리턴 타입은 무엇인지, 어떤 필드를 가지고 각각의 타입은 무엇인지, 클래스의 이름이 무엇이고 어떤 클래스를 상속하는지, 어떤 인터페이스를 구현하는지 등의 많은 정보를 가져올 수 있습니다. 이런 정보들을 가져오는 방법 이외에도, 필드의 값을 수정할 수 있고 원하는 파라미터를 이용해 메소드를 호출할 수 있습니다.

이런 리플렉션을 이용하여 private 접근자를 가지는 메소드를 테스트 할 수도 있습니다. **PowerMock** 이 대표적인데요, PowerMock을 사용하면 리플렉션을 이용해서 Private 접근자를 가지는 메소드를 호출할 수 있기 때문에 해당 메소드를 `protected` 접근자로 변경하고, 테스트 할 클래스를 상속받아 테스트 코드를 작성하는 방법을 사용하지 않을 수도 있습니다.

<br><br>

출처: 토비의 스프링 06. AOP
