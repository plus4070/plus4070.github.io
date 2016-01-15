---
layout: post
title: Hello new world!
categories: [NHN Entertainment Assignments]
tags: [Jekyll, Github, Disqus, Assignment#2]
comments: true
description: NHN Entertainment Assignments#2.
---

##Jekyll 윈도우에 설치 후 사용하기

Jekyll에 대한 설명을 확인하기 위해서는 아래의 링크를 확인하세요.

<br>

> ####*http://jekyllrb.com*

<br>
<br>

##Jekyll을 윈도우에서 사용하기 위해서 설치해야 하는 것들
 
- Ruby
- Jekyll
- Python(Setuptool, pip, Pygments)
- rouge

Jekyll은 루비 기반으로 돌아갑니다. 따라서 Ruby가 설치된 장비가 필요하고 Syntax highlighting을 사용하기 위해서는 Pygments가 있어야 합니다. Pygments는 Python을 기반으로 돌아가기 때문에 Python 또한 설치되어 있어야 합니다.

<br>
<br>

##Ruby 설치하기

Jekyll은 Ruby기반으로 작동하기 때문에 Ruby와 Development Kit을 함께 설치해야 합니다.

<br>

###Ruby 설치하기


Ruby관련 파일을 아래의 링크에서 다운받아 설치합니다.


> ####*http://rubyinstaller.org/downloads/*

<br>

![RubyInstaller](https://github.com/plus4070/plus4070.github.io/blob/master/assets/resources/img/2016-01-09-JeKyllWithGitHub/RubyInstaller.PNG?raw=true)

자신의 컴퓨터 버전에 맞게 설치를 진행하세요. 저는 x64bit 버전을 다운로드 받아서 진행하였습니다.

설치하는경우에 Add Ruby executables to your PATH 를 선택하여 실행환경에 등록할 수 있습니다.

<br>

###Development Kit 설치

Development Kit을 설치하기 위해 아래의 링크를 확인하세요.

> ####*http://rubyinstaller.org/downloads/*

설치는 아래의 링크로 설치를 하는것이 무난하게 진행할 수 있습니다. 다운받은 실행파일을 RubyDevkit폴더를 만들어 압축을 풀으세요. 

> ####*C:\RubyDevkit*

Devkit을 사용하기 위해서 초기화를 해야 합니다. Cmd창에서 아래의 명령으로 초기화와 Ruby와의 Binding을 해줍니다.

> ####*cd C:\RubyDevkit*


> ####*ruby dk.rb init*


> ####*ruby dk.rb install*


<br>
<br>

##Jekyll 설치하기

다음으로 Jekyll을 설치합니다. Ruby의 gem 패키지 인스톨러를 이용하여 설치합니다.

> ####*gem install jekyll*

<br>

code blocks 사용을 위해 rouge를 설치합니다.

> ####*gem install rouge*

<br>
<br>

##Python 설치하기

Jekyll에서 Syntax highlighter를 사용하기위해서 python을 설치 해줍니다. 아래의 경로에서 다운로드를 합니다.

> ####*https://www.python.org/downloads/*

<br>

pip는 따로 설치를 하지 않아도 Python을 설치할 때 자동으로 설치가 됩니다.

<br>

###윈도우 환경변수 설정

설치를 완료하였으면 정상적으로 설치되었는지 확인을 합니다.

환경변수에 아래의 경로를 추가해줍니다.

> ####*C:\Python27;C:\Python27\Scripts;*

![환경변수](https://github.com/plus4070/plus4070.github.io/blob/master/assets/resources/img/2016-01-09-JeKyllWithGitHub/executeSetting.PNG?raw=true)

![환경변수세팅](https://github.com/plus4070/plus4070.github.io/blob/master/assets/resources/img/2016-01-09-JeKyllWithGitHub/executeSetting1.PNG?raw=true)

<br>

Python과 pip가 정상적으로 실행되는지 Cmd창을 켜서 확인해보세요.

![실행](https://github.com/plus4070/plus4070.github.io/blob/master/assets/resources/img/2016-01-09-JeKyllWithGitHub/cmd.PNG?raw=true)

<br>

###Python&pip 실행

환경셋팅이 완료되엇으면 정상적으로 python이 실행됩니다.

>####*python*

![python](https://github.com/plus4070/plus4070.github.io/blob/master/assets/resources/img/2016-01-09-JeKyllWithGitHub/python.PNG?raw=true)

<br>

>####*pip*

![pip](https://github.com/plus4070/plus4070.github.io/blob/master/assets/resources/img/2016-01-09-JeKyllWithGitHub/pip.PNG?raw=true)

<br>

###Pygments 설치

Syntax highlighting을 사용하기 위해서 Pygments를 설치 해보도록 하겠습니다. 설치는 pip로 설치하면 간단히 설치가 됩니다.

> ####*pip install Pygments*

<br>
<br>

##Jekyll 실행하기

이제 jekyll을 실행해보도록 하겠습니다. C 아래에 Jekyll 이름을 가지는 폴더를 만들고 해당 경로로 이동합니다. (이는 편의상 하는 것이기 때문에 굳이 따라할 필요는 없습니다.)

해당 폴더로 이동하고 jekyll명령어를 통해 성공적으로 설치가 되었는지 확인해 줍니다.


> ####*cd Jekyll*


> ####*jekyll*

![jekyll](https://github.com/plus4070/plus4070.github.io/blob/master/assets/resources/img/2016-01-09-JeKyllWithGitHub/jekyll.PNG?raw=true)
<br>

jekyll이 성공적으로 설치가 되었으면 아래의 명령어를 통해서 실행시켜줍니다.

> ####*jekyll serve*

![jekyll serve](https://github.com/plus4070/plus4070.github.io/blob/master/assets/resources/img/2016-01-09-JeKyllWithGitHub/jekyllserve.PNG?raw=true)

<br>

wdm이 설치되지 않아서 오류가 발생할 수도 있으니 wdm을 설치해주도록 합시다.

>####*gem install wdm*

![gem install wdm](https://github.com/plus4070/plus4070.github.io/blob/master/assets/resources/img/2016-01-09-JeKyllWithGitHub/gemwdm.PNG?raw=true)

<br>

설치 완료 후 다시 jekyll serve명령어를 통해 jekyll을 실행시키고 127.0.0.1:4000으로 접속하시면 됩니다.

>####*127.0.0.1:4000*

<br>

아래와 같은 모양이 나오면 성공적으로 jekyll을 실행한 것입니다.

![jekyll성공](https://github.com/plus4070/plus4070.github.io/blob/master/assets/resources/img/2016-01-09-JeKyllWithGitHub/localhost.PNG?raw=true)

<br>

##Jekyll 사이트 생성하기

이제 Jekyll사이트를 생성해보도록 하겠습니다.

사이트 생성은 new 명령을 통해서 생성가능합니다.

blog라는 폴더를 만들어서 생성하겠습니다.

>####*jekyll new C:\Jekyll\blog*


>####*cd blog*


>####*dir/w*

![dir/w](https://github.com/plus4070/plus4070.github.io/blob/master/assets/resources/img/2016-01-09-JeKyllWithGitHub/dir.PNG?raw=true)
<br>

###환경설정하기
사이트를 생성하였으니 환경설정을 해야 합니다.

위에서 설치한 syntax highlighting기능과 UTF-9을 사용가능하게하겠습니다. 이를위해 설정파일을 열도록 하겠습니다. 설정파일은 _config.yml입니다.

해당 파일의 아무곳에 아래의 내용을 입력하도록 합시다.

>####*encoding: utf-8*


>####*highlighter: rouge*


>####*highlighter: pygments*

<br>

다시 jekyll을 실행하면 바뀐 결과를 확인할 수 있습니다.

>####*jekyll serve*

![jekyll serve](https://github.com/plus4070/plus4070.github.io/blob/master/assets/resources/img/2016-01-09-JeKyllWithGitHub/2016-01-09-JeKyllWithGitHub/jekyllserve2.PNG?raw=true)

<br>

아래의 Localhost주소로 바뀐 결과를 확인해보세요.

>####*127.0.0.1:4000*

![바뀐 site](https://github.com/plus4070/plus4070.github.io/blob/master/assets/resources/img/localhost2.PNG?raw=true)
<br>

[출처] WhaTap Tech Blog (와탭 기술 블로그)

<br>
<br>

<img src="https://github.com/plus4070/plus4070.github.io/blob/master/assets/resources/img/2016-01-09-JeKyllWithGitHub/blog.PNG?raw=true" />

현재 개발중인 블로그 화면

### Assignment#2 is on GitHub

<a class="btn btn-default" href="https://github.com/plus4070/plus4070.github.io">Get code in Github</a>
