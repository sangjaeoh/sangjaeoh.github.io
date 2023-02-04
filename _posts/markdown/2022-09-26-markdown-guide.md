---
layout: single
title: "마크다운 작성방법"
categories: [Markdown]
tag: [Markdown]
author_profile: true
toc: true
toc_sticky: true
---



# 마크다운이란?
마크다운(Markdown)은 일반 텍스트 기반의 경량 마크업 언어다. 일반 텍스트로 서식이 있는 문서를 작성하는 데 사용되며, 일반 마크업 언어에 비해 문법이 쉽고 간단한 것이 특징이다. HTML과 리치 텍스트(RTF) 등 서식 문서로 쉽게 변환되기 때문에 응용 소프트웨어와 함께 배포되는 README 파일이나 온라인 게시물 등에 많이 사용된다.  
\- 위키백과

<br/>

# 마크다운 문법
[마크다운 기본 가이드](https://www.markdownguide.org/basic-syntax/){:target="_black"},
[마크다운 확장 가이드](https://www.markdownguide.org/extended-syntax/){:target="_black"}
을 참고하였습니다.

## 제목
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
<br/>

## 줄바꿈
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
<br/>

## 강조
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

<br/>
<br/>


## 인용구
인용구 내용을 작성할때는 '>'를 사용합니다.
```
> 마크다운을 활용한 글쓰기
>
> 여러줄도 가능하죠.
>> 들여쓰기도 되고요.
```
> 마크다운을 활용한 글쓰기
>
> 여러줄도 가능하죠.
>> 들여쓰기도 되고요.

<br/>
<br/>


## 리스트
정렬된 목록을 사용하려면 숫자를 적어줍니다.
```
1. 첫 번째 아이템
2. 두 번째 아이템
3. 세 번째 아이템
```
1. 첫 번째 아이템
2. 두 번째 아이템
3. 세 번째 아이템

<br/>

정렬이 필요없는 리스트는 - 또는 *을 사용합니다.
```
- 첫 번째 아이템
- 두 번째 아이템
- 세 번째 아이템
```
- 첫 번째 아이템
- 두 번째 아이템
- 세 번째 아이템


<br/>
<br/>

## 코드
코드를 표현하려면 백틱(`)으로 단어를 묶습니다.
```
`String` 자료형
```
`String` 자료형

<br/>
<br/>


## 코드 블록
코드 블록을 사용하려면 백틱 3개를(```)을 사용합니다.

\`\`\`html  
코드 블록 안에 원하는 내용을 입력, 백틱 뒤에 서식을 입력할 수도 있습니다.  
\`\`\`  

```html
코드 블록 안에 원하는 내용을 입력, 백틱 뒤에 서식을 입력할 수도 있습니다.
```


<br/>
<br/>


## 가로줄
가로줄을 만들려면 세 개 이상의 별표(***), 대시(---) 또는 밑줄(___)을 한 줄에 단독으로 사용합니다.
```
***
---
_________________
```
***
---
_________________


<br/>
<br/>



## 링크
링크를 만드려면 링크 텍스트를 `[]`로 묶고 URL을 `()`로 묶습니다. `""`을 사용하여 링크에 마우스를 올릴시 타이틀을 추가할 수도 있습니다.
```
[Stack O Flow](https://sangjaeoh.github.io/)
[Stack O Flow](https://sangjaeoh.github.io/ "타이틀 추가 가능")
```
[Stack O Flow](https://sangjaeoh.github.io/ "타이틀 추가 가능")

<br/>
<br/>



## 이미지
이미지를 첨부하려면 `!`를 앞에 붙이고, 대체 타이틀을 `[]`로 묶고 이미지 경로를 `()`로 묶습니다.
```
![스마일](/assets/images/logo.png)
```
이미지 크기를 조절하려면 HTML을 사용합니다.
```
<img src="/assets/images/logo.png" alt="스마일" width="100px" height="100px">
```
<img src="/assets/images/logo.png" alt="스마일" width="100px" height="100px">

<br/>
<br/>



## 이미지 링크
링크의 `[]`안에 이미지 첨부를 합니다.
```
[![스마일](/assets/images/logo.png)](https://sangjaeoh.github.io/)
[<img src="/assets/images/logo.png" alt="스마일" width="100px" height="100px">](https://sangjaeoh.github.io/)
```
[<img src="/assets/images/logo.png" alt="스마일" width="100px" height="100px">](https://sangjaeoh.github.io/)

<br/>
<br/>




## 이스케이프 문자
이스케이프 문자는 백슬래쉬 `\`를 사용합니다.

```
\* 백슬래쉬가 없다면 리스트가 표현됨.
```
\* 백슬래쉬가 없다면 리스트가 표현됨.

<br/>
<br/>



## HTML
마크다운은 HTML 표현 태그를 지원합니다.
```
마크다운 **강조** 사용, HTML <strong>강조</strong> 사용
```
마크다운 **강조** 사용, HTML <strong>강조</strong> 사용




## Cheat Sheet
**기본 문법**  
<table>
    <thead>
        <tr>
            <th>Element</th>
            <th>Markdown Syntax</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Heading</td>
            <td>
                <code># H1 <br> ## H2 <br> ### H3 </code>
            </td>
        </tr>
        <tr>
            <td> Bold </td>
            <td>
                <code>**bold text**</code>
            </td>
        </tr>
        <tr>
            <td> Italic </td>
            <td>
                <code>*italicized text*</code>
            </td>
        </tr>
        <tr>
            <td> Blockquote </td>
            <td>
                <code>&gt; blockquote</code>
            </td>
        </tr>
        <tr>
            <td> Ordered List </td>
            <td><code>
                1. First item<br>
                2. Second item<br>
                3. Third item
                </code>
            </td>
        </tr>
        <tr>
            <td> Unordered List </td>
            <td><code>
                - First item <br>
                - Second item <br>
                - Third item
            </code></td>
        </tr>
        <tr>
            <td> Code </td>
            <td>
                <code>`code`</code>
            </td>
        </tr>
        <tr>
            <td> Horizontal Rule </td>
            <td>
                <code>---</code>
            </td>
        </tr>
        <tr>
            <td> Link </td>
            <td>
                <code>[title](https://www.example.com)</code>
            </td>
        </tr>
        <tr>
            <td> Image </td>
            <td>
                <code>![alt text](image.jpg)</code>
            </td>
        </tr>
    </tbody>
</table>

<br/>


**확장 문법**  
<table>
    <thead>
        <tr>
            <th>Element</th>
            <th>Markdown Syntax</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Table</td>
            <td><code>
                | Syntax      | Description |<br>
                | ----------- | ----------- |<br>
                | Header      | Title       |<br>
                | Paragraph   | Text        |
                </code>
            </td>
        </tr>
        <tr>
            <td>Fenced Code Block</td>
            <td><code>```<br>
                {<br>
                &nbsp;&nbsp;"firstName": "John",<br>
                &nbsp;&nbsp;"lastName": "Smith",<br>
                &nbsp;&nbsp;"age": 25<br>
                }<br>
                ```
                </code>
            </td>
        </tr>
        <tr>
            <td>Footnote</td>
            <td><code>
                Here's a sentence with a footnote. [^1]<br><br>
                [^1]: This is the footnote.
                </code>
            </td>
        </tr>
        <tr>
            <td>Heading ID</td>
            <td>
                <code>### My Great Heading {#custom-id}</code>
            </td>
        </tr>
        <tr>
            <td>Definition List</td>
            <td><code>
                term<br>
                : definition
                </code>
            </td>
        </tr>
        <tr>
            <td>Strikethrough</td>
            <td><code>~~The world is flat.~~</code></td>
        </tr>
        <tr>
            <td>Task List</td>
            <td><code>
                - [x] Write the press release<br>
                - [ ] Update the website<br>
                - [ ] Contact the media
                </code>
            </td>
        </tr>
        <tr>
            <td>Emoji</td>
            <td><code>
                That is so funny! :joy:
                </code>
            </td>
        </tr>
        <tr>
            <td>Highlight</td>
            <td><code>
                I need to highlight these ==very important words==.
                </code>
            </td>
        </tr>
        <tr>
            <td>Subscript</td>
            <td><code>
                H~2~O
                </code>
            </td>
        </tr>
        <tr>
            <td>Superscript</td>
            <td><code>
                X^2^
                </code>
            </td>
        </tr>
    </tbody>
</table>