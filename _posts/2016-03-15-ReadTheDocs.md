---
layout: post
title: ReadTheDocs?
categories: [NHN Entertainment DevDays]
tags: [ReadTheDocs, Version관리, Automation]
comments: true
description: Read the docs는 또또캐.
---

## Readthedocs? ##

> We pull your code from your Subversion, Bazaar, Git, and Mercurial repositories. Then we build documentation and host it for you. Think of it as Continuous Documentation.

`reStructuredText`나 `Markdown`문법을 이용한 Document를 만들어주는 Opensource. 개발을 진행하면서 문서작성하는 일은 기능을 명시하는 일 이외에 추가적인 부분이 너무 많음. (문서를 항상 최신의 상태를 유지하도록 하고 여러 사용자들을 위해 다른 언어로 번역하는 등의 업무가 이에 해당됨)

따라서 이러한 업무의 양을 줄이기 위해서 `Documentation`을 위한 **Read the Docs**를 만들게 되었음. 개발자는 단지 코드를 수정하고 문서만을 수정해서 올리게되면 나머지 Layout을 잡아주는 역할이나 PDF로 내보내기 기능 등을 **Read the Docs**에서 해주게 된다.

<br>

You have two options for formatting your documentation:

- In reStructuredText(sphinx)
- In Markdown(markdownpad2 etc)

<br>

### Read the Docs features ###

- GitHub and Bitbucket Integration
- Auto-updating
- Internationalization
- Canonical URLs
- Versions
- PDF Generation
- Search
- Alternate Domains

<br>

### Sphinx? ###

> Sphinx is a tool that makes it easy to create intelligent and beautiful documentation.

<br>

### Tox? ###

> Tox is the only dependency you need to run linting or our test suite, the remainder of our requirements will be installed by Tox into environment specific virtualenv paths.

<br>