---
layout: post
title: Client Performance Test with Selenium
categories: [NHN Entertainment DevDays]
tags: [Selenium, Performance Test, Client, Performance]
comments: true
description: !
---

## Selenium을 이용한 클라이언트 성능 테스트 ##

### Timer를 통한 응답 시간 측정 ###

웹 드라이버에서 수집할 수 있는 기본 측정지표 중 하나인 페이지 로딩시간이나 응답시간을 측정하는 방법을 살펴본다. 로딩시간이나 응답시간을 측정하기 위해서는 사용하는 방법에는 여러가지가 존재한다.

<br>

##### System.currentTimeMillis() #####

우선 `System.currentTimeMillis()`메소드를 이용해 로딩시간을 알아보는 방법을 살펴보자. driver를 이용해 특정 앨리먼트를 찾거나 로딩하기 전에 `System.currentTimeMillis()` 메소드로 현재시간을 변수에 저장하고, 모든 로딩이 끝난 이후의 현재시간을 또 다른 변수에 저장해서 두 개의 변수의 차를 이용해 로딩시간을 계산하는 방법을 사용한다.

```
long startTime = System.currentTimeMillis();
driver.get(URL);

new WebDriverWait(driver, TIMEOUT).until(ExpectedConditions.elementToBeClickable(By.id("ID")));

long endTime = System.currentTimeMillis();
long totalTime = endTime - startTime;

assertTrue(totalTime < 5.0);

```

이렇게 `System.currentTimeMillis()`메소드를 통해 시간차를 구해 로딩시간을 알아내는 방법은 최선이 아니지만 페이지 로딩시간 측정만을 생각하기에는 아무런 문제가 없다.

<br>

##### StopWatch 인터페이스 #####

`org.apache.commons.lang.time` 패키지에 `StopWatch` 클래스를 이용하여 인스턴스를 하나 생성하고 해당 객체의 `start()`와 `stop()` 메소드를 이용해 시간을 측정하는 방법이다.

```
StopWatch timer = new StopWatch();
timer.start();

driver.get(URL);

new WebDriverWait(driver. TIMEOUT).until(ExpectedConditions.presenceOfElementLocated(By.id("ID")));

timer.stop();
log.debug(timer.getTime()+"ms");
```
`StopWatch`클래스에서 제공하는 메소드를 통해 이벤트 간 소요시간이나 로딩시간 등을 측정할 수 있다. 그 밖에도 타이머를 일시정지하거나, 재시작하는 메소드를 사용해 유동적으로 타이머를 조절할 수 있다.

<br>

##### 네비게이션 타이밍 API #####

네비게이션 타이밍 API는 웹 성능 측정 관련 W3C 표준 자바스크립트 API이다. API를 이용하면 페이지 탐색과 이벤트 로딩에서 필연적으로 발생하는 타이밍 관련 정보를 정확하고 상세한 통계 자료로 얻을 수 있다고 한다. IE9와 크롬, 파이어폭스 등의 브라우저에서 사용이 가능하다.

이런 API의 사용은 `window.performance` 객체의 타이밍 인터페이스 프로퍼티를 통해 이루어진다.

```
driver.get(URL);
JavascriptExecutor js = (JavascriptExecutor) driver;

long loadEventEnd = (Long) js.executeScript("return window.performance.timing.loadEventEnd;");
long navigationStart = (Long) js.executeScript("return window.performance.timing.navigationStart;");

long pageLoadTime = loadEventEnd - navigationStart;

log.debug(pageLoadTime + "ms");
```

loadEventEnd와 navigationStart의 시간차는 페이지 로딩시간을 의미한다.

<br>

##### BrowserMob Proxy #####

`BrowserMob Proxy`는 웹 애플리케이션의 네트워크 트래픽을 감시하고 조작할 수 있는 무료 도구다. 애플리케이션의 성능 데이터를 HTML Archive(HAR) 형식으로 수집할 수 있을 뿐만 아니라 HTTP 요청과 응답을 제어하며, 브라우저 동작과 트래픽을 조작할 수 있다. Git 주소는 아래와 같다.

> https://github.com/lightbody/browsermob-proxy

`BrowserMob Proxy`를 다운로드하고, 셀레늄 프로젝트가 `BrowserMob Proxy`폴더 하위에 있는 lib 폴더의 jar파일을 참조하게 설정한다.

1. `BrowserMob Proxy`서버를 세팅한다.

```
//BrowserMod Proxy를 시작한다
ProxyServer server = new ProxyServer(9090);
server.start();
```

2. `BrowserMob Proxy`는 `SeleniumProxy`와 `DesiredCapabilities`객체를 사용한다.

```
//Selenium Proxy 객체를 얻는다
Proxy proxy = server.seleniumProxy();
DesiredCapabilites cap = new DesiredCapabilities();
cap.setCapability(CapabilityType.PROXY, proxy);
```

3. 프록시를 사용해 브라우저 인스턴스를 만들고 실행한다.

```
//브라우저를 실행
WebDriver driver = new FirefoxDriver(cap);
```

4. HAR 파일을 생성한다.

```
//HAR 파일을 생성하고, 라벨을 붙인다
server.newHar("HAR_NAME");
```

5. 애플리케이션과 상호작용하는 동작을 구현한다.

```
//동작의 구현
driver.get(URL);
...
```

6. `BrowserMob Proxy`서버에서 측정한 성능 데이터를 수집.

```
//HAR 데이터를 가져온다
Har har = server.getHar();

//파일에 HAR 데이터를 기록
File harFile = new File(FILE_PATH);
har.writeTo(harFile);

//BrowserMob Proxy서버를 중지
server.stop();
```
