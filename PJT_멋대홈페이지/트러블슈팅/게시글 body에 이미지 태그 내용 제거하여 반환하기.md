## 문제 상황
게시판에 단순 접근하거나 게시글 검색을 한 결과에서는 body 내용이 간소화되어서 제공되어야 한다.

문제는, 우리 게시글들은 DB 속 게시글 테이블의 body 칼럼에 html이 들어간다는 거다. 이걸 단일 게시글 조회할때엔 프론트에서 문서화해서 보여주지만, 게시글 목록에서는 body 내용을 하나하나 모두 보여주지 않고 간소화시켜서 제공해주므로, 본문 내 이미지들을 요약문에서 보여주기엔 적절하지 않다.

따라서 이를 처리해줘야 한다.

## AS-IS
아래는 게시글 목록 dto 생성자이다. 저기에 있는 저 body에 들어가는 문자열을 처리해줘야 한다.
```java
public PostSimpleData(...) {  
    this.postId = postId;  
    this.authorId = authorId;  
    this.mainCategory = mainCategory;  
    this.subCategory = subCategory;  
    this.authorName = authorName;  
    this.authorProfileImageUrl = authorProfileImageUrl;  
    this.title = title;  
    this.body = body;  
    this.thumbnailUrl = thumbnailUrl;  
    this.createdDate = createdDate;  
}
```

예를 들면 이런 식이다.
```html
<p>썸넬 전 내용</p><p><img src="https://store.blahblah.com/thumbnail.jpg"></p><p>썸넬 후 내용</p><p><img src="https://store.blahblah.com/image2.jpg"></p>
```
위 내용에 있는 내용 중 img 태그에 대한 내용을 지워줘야 한다.

### TO-BE
이를 반영하기 위해 아래와 같은 메서드를 dto 에 선언해 줬다.
```java
private String removeImageTag(String imageIncluded) {  
    String imageExcluded = imageIncluded.replaceAll("<p><img\\s.*?></p>", "");  
    return imageExcluded; // <p>썸넬 전 내용</p><p>썸넬 후 내용</p>
}
```
이렇게 해주면 위와 같이 img 태그 주변의 p 태그까지 전부 제거할 수 있다. 여기서 `.replaceAll()` 메서드의 첫 번째 parameter에는 img 태그의 형식을 정규표현식으로 지정하여 해당 문자열의 모든 이미지 태그를 제거하고자 하고 있다. 저기서 `\s`는 공백을 의미한다. 

위 정규표현식을 해석하면 다음과 같다 :
_"`<p><img`로 시작하는 문자열이면서 바로 공백이 따라오고, 어떠한 문자가 0번 이상 반복될 수 있고(하지만 최소한의 개수로 일치하는 문자), 그리고 나서는 `></p>`로 끝나는 문자열까지를 빈칸으로 대체한다."_

`.*`가 의미하는 것이 "어떠한 문자가 0번 이상 반복될 수 있다"이고, `?`가 "가장 적은 개수로 일치하는 것을 찾는다"는 정규표현식이다. img 태그가 여러개일 경우 한번에 뭉뚱그려서 정규표현식에 의해 검열될 수 있으므로, 이 효과를 구현하기 위해서는 이와 같이 `?` 처리를 반드시 해줘야 한다.

### 레슨런
(나만 몰랐던 것 같다는 느낌이 들지만) 문자열의 `.replace()`메서드의 첫 번째 parameter에 정규표현식을 넣는 개념이란걸 전혀 알고 있지 않았다. 그저 바꿔치기할 문자열을 똑같이 넣어둬야 하는 줄 알았는데, 이번에 또 하나 큰 배움을 얻게 되었다.

