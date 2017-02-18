---
layout: post
title: Custom Resolver?
categories: [NHN Entertainment DevDays]
tags: [Java, Spring, Custom Resolver]
comments: true
description: !
---

## Resolver? ##

세션 공유를 위해서 여러 가지의 방법을 찾던 중(Filter, Interceptor 등), `HandlerMethodArgumentResolver`를 이용해서 해결하는 방법을 생각하게 되었습니다.

### HandlerMethodArgumentResolver?

`HandlerMethodArgumentResolver`는 스프링 3.1 버전에서부터 추가된 인터페이스 입니다. 원래는 `WebArgumentResolver`라는 이름이었는데, 바뀌었다고 합니다!

### 그래서 Resolver가 뭔데?

Resolver는 **해결자** 라는 뜻을 가집니다. 굳이 끼워맞추자면... ArgumentResolver는 인자들을 해결해주는 역할을 한다고 생각할 수 있겟네요.

그래서 ArgumentResolver는 Controller로 요청이 넘어올 때, ServletRequest에 있는 인자값과 매핑되는 인자가 존재하면 값을 주입해주는 역할을 합니다.

비슷하게, ViewResolver는 컨트롤러에서 String 값으로 리턴을 할 때, 이름이 맞는 뷰를 찾아서 리턴해주는 역할을 합니다. 아래와 같이 선언이 되어있죠.

```xml
<beans:bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
  <beans:property name="prefix" value="/WEB-INF/views/" />
  <beans:property name="suffix" value=".jsp" />
</beans:bean>
```

이제 ArgumentResolver를 하나 만들어봅시당

### 적용방법

1. 우선, ArgumentResolver로 사용할 클래스를 만들어 줍니다. 저희는 User 관련 매핑을 해주기 위해서 `UserArgumentResolver`라는 이름으로 클래스를 만들어주었습니다.

![Inline-image-2017-02-17 17.47.33.169.png](/files/1887581617492960691)

2. Servlet 관련 설정을 하는 xml(저희조는 `mvc-config.xml`)에 추가한 Resolver의 경로를 적어줍니다.

```xml
  <annotation-driven>
    <argument-resolvers>
      <beans:bean class="com.nhnent.pickle.resolver.UserArgumentResolver"/>
    </argument-resolvers>
  </annotation-driven>
```

3. 이제 Resolver에서 할 일을 정의해줍시다. `HandlerMethodArgumentResolver` 인터페이스를 상속받아 정의된 함수들을 오버라이딩 해줍니다.

```java
public interface HandlerMethodArgumentResolver {

    boolean supportsParameter(MethodParameter parameter);

    Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer, NativeWebRequest webRequest, WebDataBinderFactory binderFactory) throws Exception;

}
```
`supportsParameter(MethodParameter parameter)` 메소드는 정의한 Resolver를 적용시킬 객체의 타입을 설정해주는 부분입니다(User).

```java
@Override
public boolean supportsParameter(MethodParameter parameter) {
    return User.class.isAssignableFrom(parameter.getParameterType());
}
```

`resolveArgument`는 컨트롤러 메소드에서 해당 타입을 가지는 객체가 인자로 들어오는 경우, 수행할 작업을 정의해 줄 수 있습니다. 저는 이 메소드에서 쿠키의 값을 읽고, 쿠키에 원하는 키 값이 들어있으면 세션서버에 조회해서 User를 리턴해주도록 하였습니다.

물론, 쿠키에 값이 없는 경우에도 들어온 Request를 확인해서 맞는 Key에 Value를 적용하고 User를 리턴해주는 ArgumentResolver의 원래 작업을 수행하는 부분도 추가해주어야 합니다!

```java
@Override
    public Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer, NativeWebRequest webRequest, WebDataBinderFactory binderFactory) throws Exception {
    HttpServletRequest request = (HttpServletRequest) webRequest.getNativeRequest();
    String sessionValue = cookieManager.findValueFromCookie(request, PSESSION_NAME);

    ...

    Enumeration<?> enums = request.getParameterNames();

    if(enums.hasMoreElements()) {
        Map<String, String> userMap = new HashMap<>();

        while(enums.hasMoreElements()) {
            String key = enums.nextElement().toString();
            String value = request.getParameter(key);

            if(value != null) {
                userMap.put(key, value);
            }
        }

        String jsonString = JSONObject.toJSONString(userMap);
        user = objectMapper.readValue(jsonString, User.class);
    }

    ...
}
```
