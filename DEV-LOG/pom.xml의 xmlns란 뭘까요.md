항상 스프링부트 + gradle 조합으로 개발하다보니, maven을 이용한 프로젝트를 진행해보지 않았었다.

이번에 싸피를 하면서 myBatis와 maven 프로젝트를 하게 될 것 같은데, 이번 기회에 pom.xml 혐오를 멈춰보고자 한다.

우선, 불편함을 지우지 위해서는 어느정도 pom.xml과 서로 알아가는 시간이 필요할 것 같아서 들여다 보기 시작했다.

gradle로 빌드 파일을 설정해오면서 봤던 의존성 설정이나 빌드 설정 등은 대략 이해가 가는데, project 태그의 xmlns 부분은 어색하게 느껴졌다.

따라서 이를 한번 알아보기로 했다.
## POM이란?

POM은 Project Object Model을 의미한다. maven으로 빌드를 진행하는 프로젝트의 설정 파일을 pom.xml이라고 하여 작성한다.

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="https://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
```
pom.xml의 상단 부분에 위와 같은 내용이 들어가게 되는데, 이게 어떤 내용을 다루고 있는지 알아보았다.

## namespace란?
xmlns는 xml namespace를 의미한다. xml은 데이터 정의 규칙을 의미하는 하나의 마크업 언어일 뿐이므로, 네임스페이스를 이해하면 위 내용을 알 수 있을 것으로 판단했다.

네임스페이스는 C++의 `using namespace std;`를 떠올리면 이해가 쉽다.

```c++
#include <iostream>

int main() {
    std::cout << "Hello, World!" << std::endl;  // cout와 endl에는 std:: 접두사가 필요함
    return 0;
}

```
위 코드는 C++로 hello world를 출력하는 코드이다. 위 코드와 아래 코드를 비교해보자.
```C++
#include <iostream>

int main() {
    using namespace std;  // 표준 라이브러리의 기능을 사용하기 위한 선언

    cout << "Hello, World!" << endl;  // cout는 std 네임스페이스에 정의된 출력 객체
    return 0;
}
```
위 두 코드를 비교하면 알겠지만, 표준 라이브러리 내에 있는 키워드 정보들을 담고 있는 것이 '네임스페이스'이며, 이를 사용하겠다 선언함으로써 위와 같이 매번 `cout` 앞에 `std::`라는 라이브러리 출처를 남겨주지 않고도 해당 코드가 표준 라이브러리에 종속되어 있는 키워드임을 컴퓨터가 이해할 수 있게 된다.

즉, 네임스페이스를 지정한다는 의미는 특정 키워드들의 출처를 반복적으로 명시하지 않고도, 컴퓨터가 바로 이해할 수 있도록 해 주는 것이다.

## xml 네임스페이스와 xml 스키마
이제 xmlns를 들여다보자.
### 1. xmlns
기본적으로 xml형식으로 파일이 작성되어야 하는데, pom.xml 설정파일 내에 있는 각 이름들이 어떤 의미를 같는지 해석할 수 있어야 할 것이다. 이처럼, xml 내의 각 태그(ex. `<modelVersion>`, `<artifactId>`)의 의미를 이해하기 위해서는 namespace를 지정해야 한다.

### 2. xmlns:xsi
그리고 xml은 구조가 있는 파일인데, pom.xml의 구조를 스키마라고 부른다. pom.xml의 구조를 지정하는 속성을 사용하기 위해서는 xml schema instance의 네임스페이스를 지정해야 하며, 이게 xmlns:xsi 이다.

### 3. xsi:schemaLocation
이제 pom.xml 스키마 파일의 위치를 지정해야 하며, 이게 xsi: schemaLocation이다. 여기서는 두 개의 경로가 공백으로 구분된다. 앞의 경로인 `https://maven.apache.org/POM/4.0.0`는 네임스페이스 URI를 의미하며, 두번째 부분인 `https://maven.apache.org/xsd/maven-4.0.0.xsd`는 실제 스키마 파일이 있는 경로를 의미한다.
