(작성중)

## 문제 상황
게시판에 단순 접근하거나 게시글 검색을 한 결과에서는 body 내용이 간소화되어서 제공되어야 한다.

문제는, 우리 게시글들은 DB 속 게시글 테이블의 body 칼럼에 html이 들어간다는 거다. 이걸 단일 게시글 조회할때엔 프론트에서 문서화해서 보여주지만, 게시글 목록에서는 body 내용을 하나하나 모두 보여주지 않고 간소화시켜서 제공해주므로, 본문 내 이미지들을 요약문에서 보여주기엔 적절하지 않다.

따라서 이를 처리해줘야 한다.

## AS-IS
아래는 게시글 목록 dto
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
