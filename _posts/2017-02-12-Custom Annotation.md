---
layout: post
title: Custom Annotation?
categories: [NHN Entertainment DevDays]
tags: [Java, Custom annotation]
comments: true
description: !
---

## Annotation? ##

어노테이션이란 JAVA5 부터 등장한 개념이다. 어노테이션은 주석이라는 의미로 해석이 되듯이, 우리가 사용하는 주석 `// or /* */`과 같이 컴파일러가 인식하지 못하게 하는 의미로 사용하기도 한다.


### ElementType

어노테이션을 사용할 때, `@Target` 어노테이션을 붙여서 개발자가 만드는 커스텀 어노테이션의 적용 범위를 지정해준다. 종류는 아래와 같다.

```java
//Class, interface (including annotation type), or enum declaration
TYPE,

//Field declaration (includes enum constants)
FIELD,

//Method declaration
METHOD,

//Formal parameter declaration
PARAMETER,

//Constructor declaration
CONSTRUCTOR,

//Local variable declaration
LOCAL_VARIABLE,

//Annotation type declaration
ANNOTATION_TYPE,

//Package declaration
PACKAGE.

//Since 1.8
//Type parameter declaration
TYPE_PARAMETER,

//Since 1.8
//Use of a type
TYPE_USE
```

### RetetionPolicy

`@Retetion`어노테이션의 value 속성으로 들어가는 Enum타입. 어노테이션의 적용 타임을 지정해준다. 종류는 아래와 같다.

```java
//Annotations are to be discarded by the compiler.
SOURCE,

//Annotations are to be recorded in the class file by the compiler but need not be retained by the VM at run time. This is the default behavior.
CLASS,

//Annotations are to be recorded in the class file by the compiler and retained by the VM at run time, so they may be read reflectively.
RUNTIME,
```

### 적용

위에서 명시한 `@Target` 어노테이션과 `@Retetion` 어노테이션을 사용하여 생성할 커스텀 어노테이션의 스코프를 지정해줄 수 있다.

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface LoginRequired {
    ...
}
```

출처
* https://docs.oracle.com/javase/8/docs/api/java/lang/annotation/ElementType.html
* https://docs.oracle.com/javase/7/docs/api/java/lang/annotation/RetentionPolicy.html
