datasource url에 localhost:3306/mydb와 같이 입력해 뒀는데, datasource url을 해석하지 못하는 에러가 발생했다.

일단 datasource url은 정확했다. (당연히 여러 원인이 있겠지만) 사실 127.0.0.1이 localhost와 동일한 의미인데, 혹시나 하고 127.0.0.1로 해보니 연결은 되는 걸 확인할 수 있었다.

아무래도 127.0.0.1을 localhost(루프백 호스트)로 인식하는 게 제대로 동작하고 있지 않는 것 같았다.


### 문제 파악하기
어디서 문제가 있나 살펴보던 중 `pom.xml`에서 `xmlns:xsi`에 `http://www.w3.org/2001/XMLSchema-instance`로 지정되어 있었는데, xsi가 http로 지정되어 있으니까 다른 xmlns 관련 url 설정들이 전부 http가 아니면 제대로 인식되지 않는 에러가 존재하는 걸 확인했다. 그래서 다음 방식을 테스트했다.
- http로 전부 수정 : 동작 O
- https로 전부 수정 : 동작 O
- xmlns:xsi를 https로 설정 -> 나머지가 어떤 프로토콜이든 동작 O
### 해결법
일단, `xmlns:xsi`는 https로 되어있으면 다른 설정값들이 어떻게 되어있든 상관이 없다. 하지만 만약 `xmlns:xsi`가 http로 되어있으면 다른 모든 xmlns관련 url들이 http 프로토콜로만 연결되어야 한다.

#### AS-IS
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
		 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		 xsi:schemaLocation="https://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
```

#### TO-BE
```xml
<project xmlns="https://maven.apache.org/POM/4.0.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="https://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
```

### 더 이해해보기
문제의 원인은 추정컨데 namespace가 제대로 연결이 되지 않아서 localhost를 127.0.0.1이라는 루프백 호스트로 매핑하지 못하는 것으로 이해되었다. 또한 xml 설정값이나 스키마 또한 제대로 해석되지 않아 설정값들이 반영되지 않으니까 index.html도 제대로 호출되지 않는 결과를 확인할 수 있었다.

위 문제 모두 xmlns나 스키마 로케이션의 적용 프로토콜을 변경해주니 해결되었다. 

### 레슨런
단순한 문제인 거 같은데, 이런 문제일수록 논리적으로 이해가 가지 않는 경우가 있을 수 있어서 해결에 어려움을 겪게 되는 것 같다. 여기서 xmlns에 대하여 알지 못했다면 이러한 원인 짐작도 어려웠을 것이다.

확실히 그 어떤 것 하나도 소홀히 하면, 언젠가 '사실은 단순한 문제'도 해결에 어려움을 겪게 될 것 같다는 생각이 든다. 내가 맡는 프로젝트의 동작 원리나 구조를 잘 이해해야 디버깅도 쉽다는 걸 제대로 느끼게 되는 계기였다.