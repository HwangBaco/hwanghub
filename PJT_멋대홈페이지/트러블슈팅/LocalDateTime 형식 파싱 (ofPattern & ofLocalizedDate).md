
제목에서 알 수 있듯, 2024-01-09 20:20:30.603778와 같이 LocalDateTime 형식의 데이터를 "yyyy. M. d" 형식으로 파싱하여 제공하려고 한다.

기획 요구사항에서 "2024. 01. 09."도 아니고, "2024. 1. 9."도 아니라 정확히 "2024. 1. 9"와 같은 형식을 요구하였기에, 해당 처리를 어떻게 할지 알아보았다.

알아보니, 이러한 형식은 자주 사용되는 형식이라 자바에서는 자주 사용하는 날짜 형식에 대하여 FormStyle이라는 enum 클래스를 운영하는 것을 알게 되었다. 내가 원하는 `DateTimeFormatter.ofLocalizedDate(FormatStyle.MEDIUM)`이라는 메서드를 지원하는 걸 알게 되었다.

그래서 아래와 같이 짰었다.

### AS-IS

```java
public String getFormattedDate() {  
    String createdDate = this.createdDate().format(DateTimeFormatter.ofLocalizedDate(FormatStyle.MEDIUM));
    int dateLength = createdDate.length();
	return createdDate.substring(0, dateLength - 1); // 2024. 1. 9
}
```

`FormatStyle.MEDIUM`은 yyyy. M. d. 형식이라 끝에 점이 더 추가되므로 이를 substring으로 제거해주려고 위와 같이 해줬다.

#### 문제는 지금부터다.

위와 같이 반환되는 걸 swagger로도 확인하고, 별도로 콘솔 출력으로도 확인하고 나서 프론트에 전달하였다. 근데 이유는 모르겠지만, 프론트에서 위 결과가 "Jan 9, 202"로 반환된다는 연락을 받았다.

일단 FormatStyle.MEDIUM으로 처리한 결과가 Jan 9, 2024로 되는 것으로 추정할 수 있었고, 끝 문자열을 잘라서 보내느라 "Jan 9, 202"로 된 것으로 파악하였다.

이상했다. 분명히 swagger 상에서도 문제 없었고, 내가 분명히 별도로 콘솔 출력으로 테스트 했을 때에도 yyyy. M. d. 형식으로 잘 되었는데, 뭐가 문제인가 싶었다.

#### 파악을 위해 자바 문서와 FormatStyle 코드부터 들어가 보았다.
자바 문서와 FormatStyle 코드에서 얻을 수 있는 정보가 동일하므로, 아래 코드 첨부로 설명을 일축하려고 한다.
```java
// Enumeration of the style of a localized date, time or date-time formatter.
public enum FormatStyle {  
    // Full text style, with the most detail.
    // For example, the format might be 'Tuesday, April 12, 1952 AD' or '3:30:42pm PST'.
    FULL,  
    // Long text style, with lots of detail.
    // For example, the format might be 'January 12, 1952'.
    LONG,  
    // Medium text style, with some detail.
    // For example, the format might be 'Jan 12, 1952'.
    MEDIUM,  
	// Short text style, typically numeric.
	// For example, the format might be '12.13.52' or '3:30pm'.
	SHORT;  
}
```
어라? 클래스 내 예시에서는 내가 테스트한 결과와 달랐다. MEDIUM의 결과가 프론트에서 제시한 "Jan 12, 1952"와 같은 형식으로 안내되어 있는 것이다.

#### 아닌데... 내가 분명히 콘솔 출력으로까지 체크해봤는데...!

결국, 위 코드에서 가장 핵심은 맨 윗줄이다. 이는 localized date를 표현하는 enum이다. 즉, 예시는 영어가 메인인 미국 기준의 출력 결과인 것으로 보인다.

궁금해서 한국 지역의 모든 형식을 출력해 봤다.

```java
String full = localDate.format(DateTimeFormatter.ofLocalizedDate(FormatStyle.FULL));  
System.out.println("full = " + full); // 2024년 1월 9일 화요일  
String longD = localDate.format(DateTimeFormatter.ofLocalizedDate(FormatStyle.LONG));  
System.out.println("longD = " + longD); // 2024년 1월 9일  
String medium = localDate.format(DateTimeFormatter.ofLocalizedDate(FormatStyle.MEDIUM));  
System.out.println("medium = " + medium); // 2024. 1. 9.  
String shortD = localDate.format(DateTimeFormatter.ofLocalizedDate(FormatStyle.SHORT));  
System.out.println("shortD = " + shortD); // 24. 1. 9.
```
신기했다. 한국어로 출력이 되는 것이다.

#### 와! 생각보다 자바가 localized 지원이 잘 되어 있구나!

즉, 프론트엔드 개발자의 환경에서는 한국 지역 값이 아니라 다른 영어권 국가 지역의 값으로 보여서 "Jan 9, 202"와 같은 결과를 얻게 된 것이다.

### TO-BE
우리 서비스에서는 "yyyy. M. d"형식으로 통일해야 했기에 이를 어느 곳에서도 공통되게 제공받을 수 있도록 `ofPattern()`메서드로 변경하여 제공하기로 했다.

```java
public String getFormattedDate() {  
    return this.createdDate().format(DateTimeFormatter.ofPattern("yyyy. M. d"));  
}
```

위와 같이 처리하면 지역에 관계없이 모두 `yyyy. M. d` 형식으로 제공받게 된다. length를 추출하여 파싱해줄 필요도 없다.

### 레슨런
만약 기획안에 따른 구현 요구사항 중 날짜 제공 부분이 각 지역에 맞게 자동으로 날짜를 변경하여 제공해도 된다면 `ofLocalizedDate()` 메서드도 좋은 선택이 될 수 있을 것으로 보인다. 구태여 국가별로 별도 처리 없이 자동으로 이루어질 테니까.

하지만 이런 변수에 흔들리지 않도록 항상 동일한 형식으로 제공해야 한다면 `ofPattern()`이 좋은 선택지일 것이다.

우연한 기회로 자바의 재밌는 기능을 알게 되어 기쁘다.