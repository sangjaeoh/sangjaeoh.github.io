---
layout: single
title: "마크다운 작성방법"
categories: [etc]
tag: [markdown]
author_profile: true
toc: true
toc_sticky: true
---



## 마크다운이란?
마크다운(Markdown)은 일반 텍스트 기반의 경량 마크업 언어다. 일반 텍스트로 서식이 있는 문서를 작성하는 데 사용되며, 일반 마크업 언어에 비해 문법이 쉽고 간단한 것이 특징이다. HTML과 리치 텍스트(RTF) 등 서식 문서로 쉽게 변환되기 때문에 응용 소프트웨어와 함께 배포되는 README 파일이나 온라인 게시물 등에 많이 사용된다.  
\- 위키백과

<hr/>

## 마크다운 문법
[마크다운 기본 가이드](https://www.markdownguide.org/basic-syntax/),
[마크다운 확장 가이드](https://www.markdownguide.org/extended-syntax/)
을 참고하였습니다.

### Headings 제목
제목을 만들려면 단어 앞에 기호(#)를 추가합니다. 
```
# Heading h1
## Heading h2
### Heading h3
#### Heading h4
##### Heading h5
###### Heading h6
```
<h1>Heading h1</h1>
<h2>Heading h2</h2>
<h3>Heading h3</h3>
<h4>Heading h4</h4>
<h5>Heading h5</h5>
<h6>Heading h6</h6>

<br/>
#### 대체 구문
텍스트 아래 줄에 제목 수준 1의 경우 == 추가, 제목 수준 2의 경우 -- 문자를 추가합니다.
```
Heading h1
===============
Heading h2
---------------
```
<br/>
<hr/>
### Line Breaks 줄바꿈
줄바꿈을 하려면 \<br/>을 사용하거나, 두개 이상의 공백(spaces)를 입력하세요.
```
첫번째 줄  
두분째 줄<br/>
세번째 줄
```
첫번째 줄  
두분째 줄<br/>
세번째 줄
<br/>
<hr/>
### Emphasis 강조
텍스트를 굵게 또는 기울임꼴로 지정하여 강조를 표현할 수 있습니다.
```
굵은 글씨
**bold text**
__bold text__

기울인 글씨
*italic text*
_italic text_

굵게 + 기울인 글씨
***bold + italic text***
___bold + italic text___
```
굵은 글씨<br/>
**bold text**<br/>
__bold text__<br/>

기울인 글씨<br/>
*italic text*<br/>
_italic text_<br/>

굵게 + 기울인 글씨<br/>
***bold \+ italic text***<br/>
___bold \+ italic text___<br/>







