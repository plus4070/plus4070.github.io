---
layout: post
title: REST API Design?
categories: [NHN Entertainment DevDays]
tags: [REST API, Design, RESTFul]
comments: true
description: !
---

# REST APIs Design

## Amazon

결론: Resource 구분에 `-(Hyphen)` 을 사용. PathVariable이나 QueryString으로 받는 경우, `_(Snakecase)`를 사용.

참고: https://docs.aws.amazon.com/apigateway/api-reference/making-http-requests/

## Facebook

결론: Resource 구분에 `-(Hyphen)`을 사용, PathVariable이나 QueryString에는 `_(Snakecase)`를 사용.

참고: https://developers.facebook.com/docs/atlas-apis/general/overview

## Github

결론: Resource에는 축약어를 사용하여 `-(Hyphen)`이나  `_(Snakecase)`를 사용하지 않음. QueryString에 `_(Snakecase)`를 사용.

참고: https://developer.github.com/v3/


## Google

결론: Resource 구분에 `-(Hyphen)`을 사용, PathVariable이나 QueryString에는 `_(Snakecase)`를 사용.

```
GET https://library.googleapis.com/v1/shelves/-/books?filter=xxx
```

parent resources에 Wild card가 적용되어야 하는 경우, URL escaping을 피하기 위해서 `*` 대신 `-`를 사용.



참고: https://cloud.google.com/apis/design/resource_names

## Twitter

결론: Resource 구분과 QueryString 등에 모두 `_(Snakecase)`를 사용.

참고: https://dev.twitter.com/rest/reference


# 추가

## Google

2010년에 이미 공공 API 들의 74%가 REST API로 이루어져 있음. 데이터의 양이 많아짐에 따라, 몇 가지의 이유로 REST API를 선호하는 사람들이 많아지고 있음.

> API services should use URL-friendly resource IDs when feasible. Resource IDs must be clearly documented whether they are assigned by the client, the server, or either. For example, file names are typically assigned by clients, while email message IDs are typically assigned by servers.


### Collection ID

* **Must** be valid C/C++ identifiers.
* **Must** be in plural form with lowerCamel case.
* **Must** use clear and concise English terms.
* Overly general terms **should** be avoided or qualified. For example, RowValue is preferred to Value. The following terms **should** be avoided without qualification:
    * Element
    * Entry
    * Instance
    * Item
    * Object
    * Resource
    * Type
    * Value

동일한 Resource를 다른 versioning을 통해서 분리할 수 있음.

### Method

[Standard Method](https://cloud.google.com/apis/design/standard_methods)와 [Custom Method](https://cloud.google.com/apis/design/custom_methods)로 구분. Standard Method는 List, Get, Create, Update, Delete 같은 API Guide를 따라야 함. Custom Method는 데이터 분석이나 데이터베이스 트랜잭션에서 사용될 수 있는 API로 기존의 RPC API들과 같이 자유 API 형태를  사용할 수 있다.

### Naming convention

API는 간단해야 함. 아래 몇 가지의 조건을 충족해야 좋은 API라고 할 수 있다.

* 많은 개발자들이 영어권이 아니기 때문에 이해하기 쉽고 의미가 명확한 단어로 이루어져야 한다.
* 공통적으로 이해할 수 있는 짧은 단어나 축약어로 이루어져야 한다.
* 직관적으로 받아들일 수 있게 가능한 친숙한 표현을 사용할 것 (ex: 어떤 resource를 삭제할 경우, erase 대신 delete).
* 같은 컨셉을 가지는 API에는 같은 단어를 사용하여 컨셉을 공유할 수 있도록 할 것.
* name overloading을 피하고 다른 컨셉에는 다른 단어를 사용할 것.
* API의 처음부분에는 특히나 더 중요하기 때문에 모호한 의미를 가지는 단어를 사용하지 말 것 (ex: /resources/...).
* programming language의 keyword와 겹치는 단어는 조심스럽게 사용할 것.

참고: https://cloud.google.com/apis/design/naming_convention

### Design Pattern

`Delete` 메소드를 통해 자원을 삭제한 경우에 반환할 것이 없다고 하더라도, Empty Response를 반환해야 한다. 현재는 아무런 데이터를 반환할 필요가 없다고 하더라도 차후에 애플리케이션이 확장되고 추가기능이 붙는다면 어떤 데이터가 필요로 할지 모르기 때문.

필드가 특정 영역을 지정하는 경우, open에는 열려있고, close에는 닫혀있는 half-open 상태로 필드의 범위가 지정되어야 한다. ex) \[start_key, end_key), \[first, last)

##### List Sub-Collections

Child Collection을 가지는 Parent Collection들 중에서, Parent에 상관없이 특정 요소를 찾고자 한다면, 부모의 id를 받는 parameter 부분을 wildcard로 지정해서 처리할 수 있다. 예를 들어, 몇 개의 선반이 있고 각각의 선반이 몇 권의 책을 가지고 있는 경우, 자신이 원하는 책을 찾는 경우에는 선반에 종속되지 않고 책들만들 대상으로 검색을 수행하여야 한다. 이런 상황에서 Parent collection의 지정범위에 `"-"`와 같은 wildcard를 사용할 수 있다. *URL escaping을 피하기 위해서 `"*"` 대신 `"-"`를 사용*

```
GET https://library.googleapis.com/v1/shelves/-/books?filter=xxx
```

### Versioning

몇 가지의 API를 제공하기 때문에 인터페이스 레벨에서 API Versioning 전략을 따르는데 `MAJOR.MINOR.PATCH`와 같은 형태를 띈다.

1. `MAJOR`: 독립적인 API의 변경이 있는 경우(신규 API 추가, 기존의 API 삭제 등).
2. `MINOR`: 사용하던 API에 신규 기능이 추가되는 경우.
3. `PATCH`: 버그 픽스.

출시 전, API 버전에 alpha나 beta와 같은 키워드가 들어가게되면, suffix로 숫자를 붙여서 동일한 버전에서의 구분을 해주어야 함.

![image.png](https://github.com/plus4070/plus4070.github.io/raw/master/img/2017-08-10-REST-API-Design/image.png?raw=true)

출처: https://cloud.google.com/apis/design/versioning
