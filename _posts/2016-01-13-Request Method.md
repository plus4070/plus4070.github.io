---
layout: post
title: Request Method
categories: [Tech]
tags: [Request Method, GET, POST, GitHub]
comments: true
description: 기술교육#1: Request Method (GET/POST)
---

# Request Method? #

#### 들어가기에 앞서 ####

OAuth에 대한 정보를 이해하기 위해서는 **HTTP Protocol**에 대한 공부가 먼저 필요하다. **HTTP Protocol**은 요청과 응답을 하는 부분으로 나누어져 있는데 각각의 라인들에 어떠한 값이 들어가는지에 대해 알고 있어야 할 것이다.

www.naver.com/logo.png를 요청하는 헤더의 일부 (Request Line)

	> GET /logo.png HTTP/1.1
	> Host: www.naver.com
	> ...

Request Header는 요청하는 Method, Request url, 요청 Protocol/Version 으로 이루어져 있다.

<br>
해당 요청에 대한 응답의 일부분 (Response Line)

    > HTTP/1.1 404 Not Found
    > ...

Response Header는 응답하는 Protocol/Version, 상태, 상태설명 으로 이루어져 있다. 

<br>

#### 메소드의 종류 ####

    Method     = "OPTIONS"                ; Section 9.2
               | "GET"                    ; Section 9.3   <-
               | "HEAD"                   ; Section 9.4
               | "POST"                   ; Section 9.5   <-
               | "PUT"                    ; Section 9.6
               | "DELETE"                 ; Section 9.7
               | "TRACE"                  ; Section 9.8
               | "CONNECT"                ; Section 9.9
               | extension-method

가장 일반적인 `GET`과 `POST` 메소드에 대해 알아보자.


`GET` 메소드는 주로 URL을 통해 이루어지는 작업에서 사용되는 메소드이다.<br>
*ex) URL을 클릭하여 발생하는 이벤트, URL에 매개변수를 입력하여 전달하는 경우, a 테그를 통한 접속*

`GET` 메소드는 매개변수에 값을 담아 전달하기 때문에 보안에 취약하고 Binary 데이터를 전달할 수 없고 전달할 수 있는 용량에 제한이 존재한다는 단점이있다. 

그에 비해 `POST`메소드는 전달하려는 값의 크기에 제한이 없고 Binary 데이터또한 전달할 수 있으며 URL을 통해 매개변수의 값을 교환하지 않으므로 보안에 좋다. 그렇지만 URL에 특정한 값이 존재하지 않기 때문에 URL을 통한 데이터의 공유는 불가능하다.

<br>

#### State ####

상태 코드는 아래와 같은 분류와 실제 상태 값을 가진다.

#### 상태분류 ####

- 1xx: Informational - Request received, continuing process
- 2xx: Success - The action was successfully received, understood, and accepted
- 3xx: Redirection - Further action must be taken in order to complete the request
- 4xx: Client Error - The request contains bad syntax or cannot be fulfilled
- 5xx: Server Error - The server failed to fulfill an apparently valid request


#### 실제 상태값(주요) ####

    Status-Code
        200 OK
		301 Moved Permanently
		302 Found
		304 Not Modified
		401 Unauthorized
		403 Forbidden
		404 Not Found
		405 Method Not Allowedad
		500 Internal Server Error
		503 Service Unavailable
