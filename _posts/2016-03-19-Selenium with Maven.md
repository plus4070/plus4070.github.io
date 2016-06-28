---
layout: post
title: Selenium with Maven?
categories: [NHN Entertainment DevDays]
tags: [Selenium, Maven, Test Automation]
comments: true
description: !
---

## Selenium? ##

**Selenium IDE**로 작성된 코드는 단순히 사용자의 클릭 이벤트, 데이터 입력등만을 가지고 작성된 코드이기 때문에 이를 customizing하기 위해서 Maven을 사용하도록 한다. 아래는 Selenium을 사용하기 위한 Dependency이다.

```xml
 <!-- Selenium -->
<dependency>
    <groupId>org.seleniumhq.selenium</groupId>
    <artifactId>selenium-java</artifactId>
    <version>${selenium.version}</version>
    <scope>provided</scope>
</dependency>
<dependency>
    <groupId>org.seleniumhq.selenium</groupId>
    <artifactId>selenium-remote-driver</artifactId>
    <version>${selenium.version}</version>
</dependency>
    
<!-- JUnit -->
<dependency>
   <groupId>junit</groupId>
   <artifactId>junit</artifactId>
   <version>${junit.version}</version>
</dependency>
```

##### 복잡한 동작 #####

자바스크립트를 테스트에 사용하거나, 복잡하고 다양한 동작을 입력하기 위해서는 ‘JavascriptExecutor’ interface나 ‘Actions’ class를 이용하여 동작을 입력해주어야 함.

참고 : [JavascriptExecutor&Actions](http://plus4070.github.io/nhn%20entertainment%20devdays/Selenium-Ext.html)

##### ID 기반 테스트 #####

테스트 수행할 때, Page의 구성이나 Element의 구조는 쉽게 바뀔 수 있으므로, Depth를 기반으로 Element를 선택하기보다는 Element의 Id나 고유한 Selector를 사용하여 Element를 선택한다. (Id나 cssSelector, 또는 xpath를 주로 사용.)

##### 에러 메시지 수정 #####

Selenium IDE를 이용해 Export된 코드는 Link로 텍스트가 직접 입력되어있거나, Element 선택이 불가능한 경우에는 아래와 같은 에러 메시지가 출력되어 있다. 이를 삭제하고 올바른 동작으로 변경해주어야 함.

```java
// ERROR: Caught exception [ERROR: Unsupported command […]]
```

##### Explicit wait & Implicit wait #####

페이지의 로딩이 끝나기 전에 페이지 내부에서 Element를 찾으면 대상 Element를 찾지 못하고 Exception이 발생한다.
이를 피하기 위해서 Explicit wait와 Implicit wait를 사용하여 해당 Element가 로드 될 때 까지 기다리는 방식으로 타이머를 생성.

```java
// Explicit wait
new WebDriverWait(driver, TIMEOUT_LITTLE_SEC).until(ExpectedConditions.elementToBeClickable(SELECTOR));

// Implicit wait
Thread.sleep(TIMEOUT_LONG_MILLISEC);
```

<br>

### 테스트 수행 ###

##### selenium-standalone-server 다운로드 #####

selenium-server를 이용하여 Hub와 Node로 이루어진 Grid를 구성할 수 있다. 아래의 주소에서 selenium-server-standalone-x.xx.x.jar 파일을 다운로드한다.

[Selenium HQ](http://www.seleniumhq.org/download/)

다운로드 받은 파일을 2개의 command 창에서 각각 아래의 명령어를 입력.

```
// Hub 생성
java  -jar  selenium-server-standalone-x.xx.x.jar -role hub

// Node 생성
java  -jar  selenium-server-standalone-x.xx.x.jar  -role node -hub http://허브IP/grid/register/ -port 노드포트번호  -browser browserName=“브라우저 이름” -Dwebdriver.브라우저종류.driver=“드라이버 경로”
```

Hub의 IP(기본포트 4444)로 접속 하면 등록된 노드의 정보가 나타나게 된다. 아래는 노드 추가시 사용할 수 있는 옵션의 종류이다.

- maxInstances : 노드에서 사용할 최대 인스턴스의 수 지정.
- maxSession : 노드에서 사용할 수 있는 최대 세션의 수 지정. 이 숫자는 maxInstances의 숫자를 덮어쓴다.
- browser : 노드에 추가할 인스턴스의 브라우저 지정. browserName으로 지정해준다.
- port : 노드의 포트번호 지정.


##### 등록 대상 Hub #####

코드 상에서 RemoteWebDriver를 생성하는 경우에는 `http://허브IP/wd/hub`를 대상으로 객체를 생성하지만 selenium-standalone-server를 이용해 노드 프로세스를 생성하는 경우에는 `http://허브IP/grid/register`를 대상으로 노드를 등록해야 한다.

Grid의 구성은 하나의 Hub에서 하나 이상의 Node들을 추가해서 사용한다. 이 때, Grid로 들어오는 요청은 Hub에서 받아서 가용한 Node를 선택하고 테스트를 수행하는 것이기 때문에 웹 드라이버 생성, 테스트 요청등은 Hub를 대상으로 진행되어야 한다.

##### Node를 등륵하는 중에 발생하는 Delay 관련 #####

Hub를 대상으로 Node를 등록하는 과정에서 딜레이가 발생할 수 있다. 아래의 파일을 수정.

```
/home1/irteam/apps/jdk/jre/lib/security/java.security
Line 70
securerandom.source=file:/dev/urandom 에서
securerandom.source=file:/dev/./urandom 으로 수정
```

<br>

### Linux 서버에서의 테스트 수행 ###

##### Xvfb 설치 #####

Linux서버에서 테스트를 수행하기 위해서는 메모리상에서 가상모니터를 띄우는 Xvfb를 설치해야 한다. 아래의 명령어로 설치한다.

```
$ yum install xorg-x11-server-Xvfb.x86_64
```

설치한 xvfb은 테스트하는 가상 서버의 옵션으로 해상도를 입력 받을 수 있다. 해상도가 너무 낮은 경우에는 페이지에서 Element를 클릭할 수 없어 ‘NoSuchElementException’이 발생할 수 있다.

```
$ xvfb-run --server-args="-screen 0, 1920x1200x24“ java –jar …
```

##### 브라우저, 드라이버 설치 #####

Firefox 브라우저 설치

```
$ yum install firefox.x86_64 : Mozilla Firefox Web browser
```

Chrome 웹 드라이버 설치 (Chrome은 브라우저와 웹 드라이버를 모두 설치해야 한다.)

[크롬 브라우저 설치](http://chrome.richardlloyd.org.uk/install_chrome.sh) <br>
[크롬 드라이버 설치](http://chromedriver.storage.googleapis.com/index.html)


참고 : [Xvfb?](http://plus4070.github.io/nhn%20entertainment%20devdays/Xvfb.html)

<br>

### Maven surefire plugin을 통한 병렬테스트 수행 ###

surefire plugin은 단위테스트를 위한 maven 플러그인이다. 아래의 Dependency를 추가한다. forkCount의 수를 변경해서 테스트를 수행할 스레드의 수를 지정해줄 수 있다.

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>${surefire-plugin.version}</version>
    <configuration>
        <forkCount>${surefire-plugin.forkCount}</forkCount>
        <reuseForks>true</reuseForks>
        <argLine>-Xmx512m -XX:MaxPermSize=256m</argLine>
    </configuration>
</plugin>
```