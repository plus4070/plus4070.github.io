---
layout: post
title: media query?
categories: [NHN Entertainment DevDays]
tags: [media query, CSS, Design, 반응형 웹]
comments: true
description: 요즘 누가 고정된 웹 페이지를 써? 당연히 반응형이지! 아님 말고
---

# media query #

## media query ##

`@media query`는 현재 CSS3의 이전 버전인 CSS2에서 나왔던 `media type`을 CSS3에서 확장한 버전이다. `media query`와 `media type`은 모두 단말기의 종류에 따라 각각 다른 스타일 시트를 적용하는 것을 목적으로 하고있다.

### media query의 적용 ###

`media query`를 적용하는 방법은 크게 3가지가 있다.

<br>

##### Mark-up에서 CSS파일 분기하기 #####

`<style></style>` 태그 안에 특정 조건 시 로드할 CSS파일을 `@import`하는 방식을 사용한다. 로드하는 방식에도 여러가지 방식이 존재하는데, 그 방식들은 다음과 같다.

- `<link>` 태그
- `@import`
- `xml`을 이용한 로드

```
<link rel="stylesheet" media="screen and (max-width:640px)" href="example.css">
<?xml-stylesheet media="screen and (max-width:480px)" rel="stylesheet" href="example.css" ?>
<style type="text/css">
	@import url(example.css) screen and (max-width:640px);
</style>
```

<br>

##### `<style>`태그에서 `@media` 선언하기 #####

`<style></style>` 태그를 선언할 때 `media`속성에 조건을 부여하고 해당 조건이 성립할 때 `<style>`태그 내의 css 구문을 실행한다. 

```
<style type="text/css" media="screen and (max-width:480px)">
	.h2{color:#00ffff}
</style>
```

<br>

##### CSS내에서 `@media` 사용하기 #####

위의 두 가지 방법들은 HTTP Request를 발생시키기 때문에 성능을 저하시키는 단점(CSS 파일 로드)과 html에서 inline으로 적용해야 하기 때문에 유지보수에 어려움이 생긴다(`<style>`태그에 `@media`선언). 따라서 세 번째 방법이 가장 많이 사용된다. 바로 CSS 파일 내에서 `@media`를 바로 사용하는 것이다.

```
(example.css)

.h2{color:#00ffff}
@media all and (max-width:480px) { .h2{color:#ff0000} }
```

<br>

### media query의 문법 ###

media query는 대소문자를 구별하지 않는다. 대표적으로 아래의 형태를 가진다.

> @media [ONLY | NOT] media_type [and 조건] * { ... }

<br>

##### Keyword 설명 #####

- `@media`는 이후에 나오는 블락단위는 `@media`의 조건을 만족할 경우 실행됨을 알려준다.
- ONLY는 media query를 지원하는 브라우저에서만 조건을 인식하도록 하는 키워드이다. media query를 지원하지 않는 브라우저에서 media query를 만나면 실행되지 않는다. 현재는 IE를 제외한 브라우저들은 대부분 media query를 지원하고 IE는 media query를 해석하지 못해서 실행을 하지 않기 때문에 ONLY자체가 큰 의미를 없지만 사용하는 것을 권장한다고 한다.
- NOT은 해당 미디어 타입을 제외한 나머지 미디어 타입에 대응할 경우에 사용한다.
- media_type은 media query를 실행할 대상 미디어 타입을 명시하는 조건이다. media_type 키워드는 다음과 같다.


| Keyword | media_type |
| --- | --- |
| all | 모든 미디어 타입 |
| aural | 음성 합성 장치 |
| braille | 점자 표시 장치 |
| handheld | 손으로 들고 다니면서 볼 수 있는 작은 스크린에 대응하는 용도 |
| print* | 인쇄 용도 |
| projection | 프로젝터 표현 용도 |
| screen* | 컴퓨터 스크린을 위한 용도 |
| tty | 디스플레이 능력이 한정된(ex. terminal) 글자를 사용하는 미디어를 위한 용도. tty에서는 px단위를 사용해서는 안됨 |
| tv | 음성과 영상이 동시 출력되는 TV와 같은 장치 |
| embossed | 페이지에 인쇄된 점자 표시 장치 |
(*는 자주 사용하는 Keyword임을 표시)

<br>

##### 조건 #####

media query의 조건에는 다른 프로그래밍이 사용하는 연산자(>, <, <=, >=...) 등을 사용하지 않고 CSS에서 사용하는 `:`만을 사용한다. 대신 `min`이나 `max`와 같은 키워드를 사용하여 비교가 가능하다.

예를 들어 `(min-width:640px)`이 조건인 경우에는 디바이스에서 지원하는 최소 해상도가 640px이상임을 의미한다. 즉, 해상도가 640px이상인 경우에만 해당 media query를 수행한다. 조건에서 사용하는 Keyword들은 아래와 같다.


| Keyword | description |
| --- | --- |
| device-width, device-height | 기기의 가로, 세로 너비 |
| min-device-width, min-device-height | 기기의 최대 가로, 세로 너비 |
| max-device-width, max-device-height | 기기의 최소 가로, 세로 너비 |
| width, height | 가로, 세로 너비 |
| min-width, min-height | 최소 가로, 세로 너비 |
| max-width, max-height | 최대 가로, 세로 너비 |
| orientation | 화면 회전(portrait, landscape를 옵션으로 사용) |
| aspect-ratio | 화면 비율(가로/세로) |
| min-aspect-ratio | 최소 화면 비율 |
| max-aspect-ratio | 최대 화면 비율 |
| device-aspect-ratio | 단말기의 물리적인 화면비율 |
| min-device-aspect-ratio, max-device-aspect-ratio | 단말기 물리적인 최소, 최대 화면 비율 |
| color, min-color, max-color | 색상 당 비트 수(자연수) |

(추가로 더 많은 조건이 존재한다)

위의 조건들을 사용해서 작성하는 media query는 다음과 같다.

```
@media only screen and (min-width: 640px) {
	...
}
// media query를 지원하는 브라우저에서 스크린(최소 640px이상)에 출력하는 경우에 실행
```


