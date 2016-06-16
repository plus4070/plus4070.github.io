---
layout: post
title: Selenium Extended(JavascriptExecutor&Actions)
categories: [NHN Entertainment DevDays]
tags: [Selenium, Automation, Test, javascript test, Actions]
comments: true
description: !
---

## JavascriptExecutor? ##

`JavascriptExecutor`는 자바스크립트 코드를 테스트하기 위해 **Selenium WebDriver**에서 제공하는 **인터페이스**이다. 아직 Selenium WebDriver에서 제공하지 않는 동작도 `JavascriptExecutor`를 통해서 만들 수 있다.

<br>

### 사용 예(링크의 수를 반환하는 자바스크립트 코드) ###

```java
@Test
public void testJavaScriptCalls() {
    WebDriver driver = new ChromeDriver();
    driver.get("http://www.google.com");
    JavascriptExecutor js = (JavascriptExecutor) driver;

    String title = (String) js.executeScript("return document.title");
    assertEquals("Google", title);

    long links = (Long) js.executeScript("var link = document.getElementByTagNames('A'); return links.length");
    assertEquals(42, links);

    driver.close();
}
```
**Selenium WebDriver**는 드라이버 인스턴스를 이용해 `JavascriptExecutor`를 생성한다. `executeScript()`메소드를 사용하면 실행 중인 자바스크립트 코드에도 인자 값을 전달할 수 있다.

<br>

## Actions class? ##

복합적인 이벤트를 정의할 때 사용할 수 있는 클래스이다. 드래그 앤 드랍, 키보드를 누른 상태에서 마우스를 클릭하는 이벤트도 이 `Actions` 클래스를 이용해 구현할 수 있다.

<br>

### 사용 예(CONTROL 키를 이용한 다중선택) ###

```java
@Test
public void testRowSelectWithControlKey() {
    List<WebElement> tableRows = driver.findElements(By.xpath("//table[@class='iceDatTbl']/tbody/tr"));

    //복합적인 이벤트를 발생시킬 Actions 클래스 인스턴스 생성
    Actions builder = new Actions(driver);

	//컨트롤 키를 이용해 2행과 4행을 선택하는 이벤트 발생    
    builder.click(tableRows.get(1)).keyDown(Keys.CONTROL)
            .click(tableRows.get(3)).keyUp(Keys.CONTROL)
              .build().perform();

	//테이블에서 2개의 행이 선택되었는지를 확인
    List<WebElement> rows = driver.findElements(By.xpath("//div[@class='icePnlGrpexampleBox']"
            + "/table[@class='iceDatTbl']/tbody/tr"));
    assertEquals(2, rows.size());
}
```

`Actions`클래스는 driver 클래스 인스턴스를 전달해 인스턴스를 생성한다. 이렇게 생성된 `Actions`클래스의 인스턴스는 `CONTROL`키 뿐만 아니라 다른 키보드의 키들 제공한다.

혼합해서 사용하는 이벤트는 `build()`메소드를 이용해 실행 준비를 완료한 후에, `perform()`메소드를 이용해 하나의 동작으로 실행한다.

<br>

### 사용 예(더블클릭) ###

```java
@Test
public void testDoubleClickEvent() {
    WebDriver driver = new ChromeDriver();
    driver.get("http://dl.dropbox.com/u/55228056/DoubleClickDemo.html");
    WebElement message = driver.findElement(By.id("message"));

    //배경색이 파란색인지 확인
    assertEquals("rgb(0, 0, 255)", message.getCssValue("background-color").toString());
    Actions builder = new Actions(driver);
    builder.doubleClick(message).build().perform();

    //배경색이 노란색인지 확인
    assertEquals("rgb(255, 255, 0)", message.getCssValue("background-color").toString());
    driver.close();
}
```

`doubleClick()`메소드를 사용하기 위해서는 타깃 엘리먼트가 필요한데, `message`를 넘겨서 타깃을 지정해준다.
