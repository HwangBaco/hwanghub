
게시글 수정 API를 작성하고 있었다.

게시글 수정을 구현하기 위해서는 다음 정보들을 한 번에 받는다.

### AS-IS
```java
public record PostUpdateRequestDto(  
    String title,  // 제목
    String body,   // 내용
	String thumbnail,  // 썸네일
    String category // 게시글 카테고리  
) {  
}
```

위 dto properties의 유효성 조건은 다음과 같다.
- 제목과 내용, 카테고리는 비어있으면 안된다.
- 썸네일은 없으면 기본 이미지로 제공된다.

이런 유효성 검사는 javax(or jakarta)에서 기본적으로 @NotNull, @NotEmpty, @NotBlank라는 어노테이션을 지원한다. 그렇다면 이 세가지 중 어떤걸 써야 할까?

#### 영어를 한국어로 바꾸니까 모호하게 느껴졌어요.
문제는 내가 한국인이기 때문에 발생했다. empty와 blank가 내 기준에선 똑같은 의미처럼 느껴졌기 때문이다.

Not Empty? 비어있지 않다?! Not Blank...? 빈칸이 아니다?

결국 찾아보게 되었는데, 단순하게 아래와 같이 정리된다.

| 분류 | 내용 |
| ---- | ---- |
| @NotNull | null인지 아닌지 체크 |
| @NotEmpty | null인지 아닌지 && ""인지 아닌지 체크 |
| @NotBlank | null인지 아닌지 && ""인지 아닌지 && " "와 같이 공백으로 채워진 문자열인지 |

 이렇게 내용을 알고 보면 감이 바로 온다. 익숙해지면 자연스럽게 외워지겠지 싶은데, 당장 오랜만에 보니까 갑자기 헷갈려서 정리하게 되었다.

### TO-BE
위 내용을 바탕으로 아래와 같이 정리하였다.

```java
import javax.validation.constraints.NotBlank;

public record PostUpdateRequestDto(  
    @NotBlank  
    String title,  
    @NotBlank  
    String body,  
    String thumbnail,  
    @NotBlank  
    String category
) {  
}
```
