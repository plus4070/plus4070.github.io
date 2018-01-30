---
layout: post
title: REST API Design?
categories: [NHN Entertainment DevDays]
tags: [REST API, Design, RESTFul]
comments: true
description: !
---

# REST API

REST는 Representational State Transfer의 abbreviation으로, HTTP로 통신하는 웹 환경에서 요청 자체에 의미를 주고 사람이 이해하기 쉽게, HTTP의 장점을 잘 살려서 활용하자는 목적을 가지고 소개된 개념이다.

## REST의 제한조건

* Client/Server architecture: 일관적인 인터페이스로 분리
* Stateless: 각 요청 간 클라이언트의 상태가 저장되어서는 안됨
* Cacheable: 클라이언트는 응답을 캐싱해서 사용, 성능 향상을 목적으로 함
* Layred system: 계층화 구조를 통해 시스템 규모 확장성을 도모하거나 로드밸런싱, 공유캐시 기능등을 제공할 수 있도록 함
* Code on demand: 서버가 클라이언트가 실행할 수 있는 로직을 전송하여 기능을 확장
* 인터페이스 일관성: 아키텍쳐를 분리, 독립적으로 만들어 각각의 부분이 독립적으로 개선될 수 있도록 함

## API Design

REST API를 설계하기 위해서는 제공하려는 자원(Resource)를 정의하고, 이를 URI로 표현할 수 있어야 한다. 많이들 보았을테지만, User의 정보와 관련된 CRUD를 수행하는 API의 경우, 요청자(Client, 이하 클라이언트)가 관심있어하는 자원은 User이고, 이를 URL에 계층적으로 표현할 수 있어야 한다.

또한, REST API는 클라이언트가 요청하는 행위를 HTTP.Method로 표현하여, 해당 API를 어떤 목적으로 사용하려는지에 대해 쉽게 식별할 수 있다.

```
HTTP/1.1

GET /users/1        //회원 1의 정보를 가져옴
POST /users/2       //회원 2를 추가
DELETE /users/2     //회원 2를 삭제
PUT /users/1        //회원 1의 정보를 수정
...
```

그러나 실제 개발을 진행하는 과정에서 위의 경우처럼, 관리하려는 자원이 예쁘게 나뉘어지는 경우는 많지 않다. 그리고 요청하는 자원이 하나의 단어로 표현되지 않는 경우 또한 허다하다.

이런 경우, API는 어떻게 디자인해야 할까. 아래는 몇 가지 대표 회사들의 API 구조를 조사한 결과이다(abc 순).

### Amazon

Resource 구분에 `-(hyphen)` 을 사용. PathVariable이나 QueryString으로 받는 경우, `_(snake_case)`를 사용.

참고: https://docs.aws.amazon.com/apigateway/api-reference/making-http-requests/

### Facebook

Resource 구분에 `-(hyphen)`을 사용, PathVariable이나 QueryString에는 `_(snake_case)`를 사용.

참고: https://developers.facebook.com/docs/atlas-apis/general/overview

### Github

Resource에는 `축약어`를 사용하여 `-(hyphen)`이나 `_(snake_case)`를 사용하지 않음. QueryString에 `_(snake_case)`를 사용.

참고: https://developer.github.com/v3/

### Google

Resource 구분에 `-(hyphen)`을 사용, PathVariable이나 QueryString에는 `_(snake_case)`를 사용.

참고: https://cloud.google.com/apis/design/resource_names

### Twitter

Resource 구분과 QueryString 등에 모두 `_(snake_case)`를 사용.

참고: https://dev.twitter.com/rest/reference


## 적용

위에서 조사한 것과 같이, API를 디자인하는 방법은 본인이 정하기 나름이다. 어떻게 더 효율적으로 자원을 URI로 표현하는지에 대한 기준을 본인이 정한다면, 해당 **자원을 조작하기 위한 API들의 규칙을 일관성있게 표현하면 된다**(그렇지만 위의 회사들에서 공식으로 제공하는 API 라고 하더라도 일관성이 지켜지지 않은 경우가 많았다).

Google의 경우가 제일 깔끔하게 API를 표현했다고 생각하는데. 예를 들어, User에 상관없이 User들이 신고있는 신발의 정보를 가져오는 API가 필요하다고 가정해보자.

```
GET /users/shoes
```

클라이언트가 필요한 정보는 users의 shoes 정보이기 때문에 위와 같이 URL을 디자인 할 수도 있다. 그렇지만 무언가 조금 어색한 것 같이 느껴진다면, 아래의 경우를 보자.

```
GET /users/-/shoes
```

기존에 User의 정보를 가져오거나 삭제, 수정하는 API 에서는 요청을 통해 로직을 수행할 대상이 {user_id}로 명확하게 지정되어 있었기 때문에, `/users/1`과 같은 표현으로 깔끔하게 표현할 수 있었다. 그렇지만 모든 User의 어떤 정보를 가져오는 API의 경우, 이 {user_id}의 표현이 애매하다.

Google에서는 이를 `-`으로 특정 자원의 ID를 나타내는 부분을 wildcard 처리하였는데, 이런 표현이 더욱 구조적으로 보인다.

## Version 관리

본인이 개발한 API가 프로젝트 내부에서만 호출되는 것이 아닌, 외부의 여러 시스템들에서도 호출하고 있다면, Version 관리는 필수적이다. 요구사항의 변경으로 인해, 응답으로 주는 데이터의 형상이 변경되어야 할 필요가 있거나, 코드를 리팩토링하는 경우, 구조 변경으로 인해 URL의 분리가 필요한 경우가 있을 수 있기 때문이다.

이런 경우, 무작정 데이터의 형상을 변경하거나 URL을 변경하는 경우에는, 외부 시스템에서 호출이 실패할 수 있기 때문에, 기존의 API를 유지하고 신규로 API를 내보내는 방식으로 진행되어야 한다. 그러나 요청하는 자원이 비슷하고 데이터 형상만 바뀌기 때문에 version만을 올려서 유사한 형태로 API를 개발하는 방법이 일반적이다.

API의 version을 관리하는 방식은 일반적으로 4가지 방법이 있는데, 아래의 방법들 말고도 다른 방법이 있을 수 있다.

### URL에 API의 version을 명시하는 방법

```
GET /api/v1/users/1
GET /api/v1/users/-/shoes
```

가장 일반적인 방법으로 호출되는 URL 자체가 API의 version을 표시하고 있는 경우이다. 이 방법은 API의 현재 version을 명시적으로 알기 쉽고, GET 요청의 경우, Postman과 같은 HTTP 요청 테스트를 위한 도구를 사용하지 않고서도, 브라우저에서 version 별 테스트를 수행할 수 있다는 장점이 있다.

### Accept header에 version을 명시하는 방법

```
GET /api/users/1
...
Accept: application/api.v1 + json
...
```
HTTP 요청을 보내는 경우, header의 Accept에 호출하는 API의 version을 명시하는 방법이다. URL을 버전에 크게 신경쓰지 않고 동일하게 유지되기 때문에 버전이 변경되더라도 Request header의 값만 조정해주는 방식으로 API 변경을 최소화할 수 있다.

### Custom Request Header로 version을 명시하는 방법

```
api-version: 2.0
```

위의 방법과 유사하게 Request header에 API의 version을 명시하는 방법인데, Accept의 경우, HTTP Request Header에 존재하는 속성에 version을 추가했다면, 이 방법은 본인이 version용으로 사용할 속성을 header에 직접 추가하여 version 용으로만 사용하는 방법이다. 이전에는 Custom 속성임을 명시하기 위해서 `X-`라는 prefix가 들어갔지만, 요새는 이런 접두사 없이 Custom header 속성을 추가하는 방법을 선호한다.

### Request parameter로 version을 지정하는 방식

```
/api/users/1?version=1.0
/api/users/-/shoes?version=1.0
```
마지막은 API의 요청 시, parameter에 version을 명시하는 방법이다.

## Version의 변경

위에서 살펴본 Version은 본인이 선호하거나 요구사항에 맞는 방식으로 API를 개발하면 될 것이다. 설명에서는 version을 모두 `v1`과 같이 major_version만 표기 했는데, 시스템이 얼마나 큰 지, 변경이 잦은지에 따라 minor_version, patch_version 등을 추가할 수 있다.

```
v{major_version}.{minor_version}.{patch_version}
```

각 version은 개발자가 정해놓은 규칙에 따라서 변경되는데, 일반적으로는 요청/응답 형상이 어떻게 변하느냐에 따라 올릴 version을 정한다.

### major_version

* 요청 및 응답 형상이 이전 버전과 호환되지 않는 경우
    * 요청 parameter에 필수 parameter 추가
    * 응답에 일부 field 삭제
* 신규 기능이 추가되는 경우

### minor_version, patch_version

* 요청 및 응답 형상이 이전 버전과 호환되도록 형상이 변하는 경우
    * 요청 parameter에 optional한 parameter 추가
    * 응답에 일부 field 추가
* Bug fix

## Example for Spring 4.x

그럼 API 별로 version을 지정하여 나누면 실제 코드 로직에서는 어떻게 구분되어야 할까. 아래 내용은 개발자마다 성향이 다르기 때문에, 본인만의 방법이 있다면 참고하지 않아도 된다.

User의 정보를 관리하는 API를 관리하는 UserController에서는 공통된 부분을 클래스에서 `RequestMapping`으로 명시하고, 각 version별 상이한 로직을 Service 객체에서 분리하여 version 별 형상에 맞게 데이터를 가공하면 된다.

```java
//UserController.java

@RestController
@RequestMapping("/api/{version}/users")
public class UserController extends BaseController<UserService> {

    @Autowired
    @Qualifier("UserServiceV1")
    private UserService userServiceV1;

    @Autowired
    @Qualifier("UserServiceV2")
    private UserService userServiceV2;

    @Override
    public Optional<UserService> apiService(final String version) {
        if (version.equals(V1)) {
                return Optional.of(userServiceV1);
        } else if (version.equals(V2)) {
                return Optional.of(userServiceV2);            
        } else {
            return Optional.empty();
        }
    }

    ...

    @GetMapping("/{user_id}")
    public Response<ResponseData<User>> getUser(@PathVariable("version") final String version
                                                @PathVariable("user_id") final String userId) {
        return apiService(version).orElse(userServiceV2).getUser(userId);
    }
}
```
