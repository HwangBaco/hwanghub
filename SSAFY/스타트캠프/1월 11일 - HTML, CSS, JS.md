
## HTML
HTTP는 HTML을 전송하는 상용 프로토콜

HTML은 HyperText를 지원하는 마크업 랭귀지임
-> 하이퍼텍스트란 텍스트에 페이지 링크를 연결하여, 텍스트를 누르면 다른 페이지로 연결되는 텍스트

web server는 정적 리소스만을 전달하는 서버

html을 동적으로 생성하는 서버가 was
-> 웹 서버에는 html을 생성하는 엔진이 있어야 함.(혹은 미들웨어라고 부르기도 함)
(servlet engine)
-> DB에 다녀와서 html을 생성해주는게 was
WAS와 Web Server는 다른 개념

#### HTTP 특징
1. 단방향 통신
HTTP가 만들어진 때에는 네트워크 속도가 100bps(byte per second) 정도였음

따라서 이 때에는 클라이언트 입장에서는 필요 데이터를 서버가 막 밀어넣는 것을 좋아할 리 없다. 왜냐하면 속도가 너무 느리니까 로딩하다가 한 세월 쓰는거다.

따라서 http 프로토콜은 server push를 못 하게 만들었다. 즉, 클라이언트가 요청을 해야만 응답을 받을 수 있게 되어있는 거다.

이걸 해결하려고 과거에 설치/사용했던 게 active x였음

2. stateless
통신 request / response를 마치고 나서는 연결을 종료함으로써 각 통신이 독립적으로 수행됨을 의미한다. 즉, 클라이언트는 연결 상태를 유지하지 않고 계속 일회성으로 연결을 수립하고 마치는 거다.

따라서 stateless를 극복하기 위한 수단으로 session, cookie가 있다. 이러한 클라이언트의 정보를 client가 가지고 있는 걸 cookie이고, 클라이언트의 정보는 server가 가지고 있는 걸 session이라고 이해하면 된다.

## HTML

- element
tag는 element라고 칭함
- attribute
element는 attr 설정이 가능함
- comments
`<!-- -->`를 이용하여 주석 처리 가능함


- block level element
	- 대부분 inline 요소와 text 요소를 포함
	- 일부 요소는 block 요소를 포함함
	- 새로운 행에 표시
	- ex) div, p, h1, form, table, li, ...
- inline level element
	- 오직 inline 요소와 text 요소만 포함
	- text 처럼 취급됨
	- 새로운 행에 표시되지 않음
	- ex) span, a, img, input, label, ...

너무 div만 남발하다 보니 구조를 이해하기 어려워서 semantic tag(의미를 담은 태그)들을 추가한 것임 (영어사전 : semantics 의미론)
- `<article>`
	- 문서의 독립적인 부분을 구성하는 섹션
	- 위젯 등 독립적인 아이템
	- ex) News, Blog, Post, Article
	- article 요소를 중첩할 경우 외부 article 요소와 연관된 내용
- `<section>`
	- 일반적인 문서 또는 프로그램의 섹션
	- 제목으로 시작하는 컨텐츠의 의미적 그룹
- `<nav>`
	- 네비게이션 링크로 구성된 섹션
	- 다른 페이지 또는 동일 페이지의 다른 섹션 연결
`<pre>`
- preformatted text
- html에서는 줄 바꿈을 `<br>`이용해줘야 함. 하지만 마크업에서는 `\r\n`와 같은 것으로 표현함
- 마크업의 한 칸 이상 공백 문자와 줄 바꿈 문자를 보존함
- 마크업의 한칸 이상 공백은 브라우저에서 한칸으로 표현됩니다. 두칸이상의 공백을 표현하고 싶다면 `<pre>`를 사용해야 합니다. [출처](https://webstoryboy.co.kr/1719)
- 근데, pre나 br 태그는 css로 컨트롤하기 어려우므로, 실제에서는 div 태그 등을 이용해서 여백이나 엔터를 컨트롤함

출처: [https://webstoryboy.co.kr/1719](https://webstoryboy.co.kr/1719) [WEBSTORYBOY:티스토리]

---
#### 참고:

Enter 입력 표현은 OS마다 다름
- 리눅스 계열은 `\r` (캐리지 리턴)만
- 윈도우는 `\r\n` (캐리지 리턴 + 라인 피드(=뉴라인)) 둘다
-> 그래서 맨날 git bash에서 찡얼거림

이를 html에서 구현하려면 `<br>`태그로 해줘야 함


---

### 절대 경로 vs 상대 경로

- 절대 경로
	- file system : "드라이브부터" 파일이 있는 실제 경로를 모두 표현 (file:///C:/SSAFY/work-front/saffy.png)
	- network : "프로토콜, 도메인부터" 파일이 있는 실제 경로를 모두 표현 (https://www.naver.com/blah/blah)
- 상대 경로
	- 특정 경로를 기준으로 파일이 있는 relative한 위치를 표현 ("./ssffy.png")
	- `..` 상위 
	- `./` 현재(생략 가능) 
	- `/fdsa` 하위 경로 
	- `~`: `file:///C:/Users/{현재 유저}/`와 동일한 의미
	- 웹 주소는 브라우저에 표시되는 도메인 주소가 기준임. (중요)
		- 예를 들어 https://naver.com:8080/context-path/path1/path2 가 있다고 할 때, 상대 경로는 port 까지를 기준으로 하여 상대 경로를 작성하면 된다.
		- 웹에서는 파일 서버를 구축하는 경우를 빼놓고는 절대 경로는 잘 안 씀. 그래서 상대 경로를 잘 이해해야 함 (기본적으로 상대 경로 위주로 사용한다는 의미)
	- 앱 주소는 프로그램이 시작하는 위치가 상대 경로의 기준이다.
	- 자바의 클래스패스는 src 기준으로 하는 것이 좋음(이클립스는 이러한 경로 지원이 달라서 개발할 때랑 실제 배포한 때랑 경로가 상이할 수 있음)

## CSS

- 외부, 내부, 인라인 적용 스타일이 존재할 때, 인라인 > 내부 > 외부 순으로 스타일 적용 우선순위를 가진다.
	- 외부 스타일시트 : head 태그 안에 `<link rel="stylesheet ...>`
	- 내부 스타일시트 : head 안에 `<style type="text/css"> body {margin: 0; padding: 0}...`
	- 인라인 스타일시트 : body 내부의 태그 안에 `<p style="color:green">hello</p>`
- 같은 우선순위일 때에는 가장 아래에 있는 스타일이 먹혀진다.
- 선언 방식은 아래와 같다.
	```css
	p {
		font-size: 20px;
	}
	#id {
		color: black;
	}
	.class {
		colod: red;
	}
	```
	활용할 수 있는 건 tailwind, bootstrap, ant design 등이 있다. ant design은 커스텀하면서 사용하기 어렵다고 하고, tailwind 권장

수업에서는 부트스트랩을 사용, 사용법은 거의 비슷한데 tailwind가 조금 더 이쁜거같아서 나름 잘 사용해보시길