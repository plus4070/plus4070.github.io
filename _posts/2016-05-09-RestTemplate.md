---
layout: post
title: Rest하게? RestTemplate!
categories: [NHN Entertainment DevDays]
tags: [Spring, Rest, RestTemplate, Web, Http]
comments: true
description: Rest하게? RestTemplate!
---

## RestTemplate? ##

`Rest`서비스를 연동하기 위해서 스프링에서 제공하는 객체이다. `restTemplate`을 사용하면 Http method인 `GET`, `POST`, `HEAD`, `OPTIONS`, `DELETE`, `PUT` 메소드로 메세지를 전송할 수 있다.

`restTemplate`을 생성하기 위해서는 `applicationContext.xml`에서 bean으로 등록하거나 사용하려는 클래스에서 생성자를 통해 생성할 수 있다.

##### xml #####

```xml
<bean id="restTemplate" class="org.springframework.web.client.RestTemplate">
	<constructor-arg>
		<list>
			<bean class="org.springframework.http.converter.ByteArrayHttpMessageConverter"/>
			<bean class="org.springframework.http.converter.StringHttpMessageConverter">
				<constructor-arg value="UTF-8"/>
			</bean>
			<bean class="org.springframework.http.converter.ResourceHttpMessageConverter"/>
			<bean class="org.springframework.http.converter.xml.SourceHttpMessageConverter"/>
			<bean class="org.springframework.http.converter.support.AllEncompassingFormHttpMessageConverter"/>
			<bean class="org.springframework.http.converter.json.MappingJacksonHttpMessageConverter"/>
		</list>
	</constructor-arg>
</bean>
```

##### java #####

```java
RestTemplate restTemplate = new RestTemplate();
```

생성자에 Bean으로 등록한 `MessageConverter`를 선언할 경우 선언한 Converter만 지원한다. 아무것도 선은하지 않을 경우에는 아래의 `HttpMessageConverter`들을 지원한다.

```
ByteArrayHttpMessageConverter : 바이트배열 처리
StringHttpMessageConverter : 문자열 처리
ResourceHttpMessageConverter
SourceHttpMessageConverter
AllEncompassingFormHttpMessageConverter
```
추가로 다른 Converter들을 지원하고싶으면 jar파일을 추가해야 한다. 아래는 jar파일을 추가해서 지원할 수 있는 Converter들 이다.

```
AtomFeedHttpMessageConverter
RssChannelHttpMessageConverter
MappingJackson2XmlHttpMessageConverter 나 Jaxb2RootElementHttpMessageConverter
MappingJackson2HttpMessageConverter 나 GsonHttpMessageConverter
```

### RestTemplate의 사용 ###

`RestTemplate`을 사용하기 위해서는 요청을 받을 API 서버주소, 요청을 보낼 Http method, 정보를 담을 HttpEntity, 그리고 응답을 받을 클래스인 ResponseClass와 요청 파라미터로 추가할 restParameter를 `exchange()`의 인자로 넣어주면 된다.

```java
restTemplate.exchange(URL, HttpMethod, HttpEntity, ResponseClass, RestParameter);
```

요청을 담을 HttpEntity는 Header와 Body를 만든 이후에 파라미터로 넣어줄 수 있다. 아래는 HttpEntity의 Header와 Body를 만드는 메소드이다.

```java
private HttpHeaders getHeaders() {
	HttpHeaders headers = new HttpHeaders();
	headers.setContentType(MediaType.APPLICATION_JSON);
	
	return headers;
}

public HttpEntity<String> getEntityBodyParameter(Object object) {
	try {
		HttpHeaders headers = getHeaders();
		String body = jsonUtils.getJsonString(object);
		return new HttpEntity<String>(body, headers);
	} catch (Exception e) {
	} 
	return null;
}

...

HttpEntity<String> httpEntity = apiClient.getEntityBodyParameter(object);
```

<br>

`RestTemplate`은 Http method에 따라 사용할 수 있는 메소드를 제공하는데 그 종류는 아래와 같다.

```
* DELETE : delete()
* GET : getForObject(), getForEntity()
* HEAD : headForHeaders()
* OPTIONS : optionsForAllow()
* POST : postForLocation(), postForObject()
* PUT : put()
* any : exchange(), execute()
```

