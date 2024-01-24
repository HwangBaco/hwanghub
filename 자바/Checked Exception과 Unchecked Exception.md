
개발하다보면 "예외처리"는 필수죠. Java의 Exception은 크게 Checked Exception과 Unchecked Exception 두 가지로 나뉘는데, 각각이 무엇인지 알아봅시다.

## 1. Checked Exception

Checked Exception은 컴파일러가 컴파일 시점에 체크하는 예외를 의미합니다. 이러한 예외들은 반드시 예외 처리를 해주어야 합니다. 예를 들어, FileNotFoundException, IOException 등이 있습니다. 이런 예외들은 파일을 읽거나 쓰는 과정에서 발생할 수 있는 **예외를 대비하여 개발자가 미리 예외처리를 해두어야 합니다**.

```java
try {
    // 예외가 발생할 가능성이 있는 코드
} catch (FileNotFoundException e) {
    // 예외 처리 코드
}
```

**2. Unchecked Exception**

반면에, Unchecked Exception은 런타임 시점에 발생하는 예외입니다. 이러한 예외들은 컴파일 시점에 체크되지 않으며, 주로 개발자의 실수로 인해 발생합니다. NullPointerException, ArrayIndexOutOfBoundsException 등이 대표적인 예입니다. 대부분 이러한 예외는 논리적 결함에서 발생하므로 **기본적으로는 예외 처리로 무장하기보다는 논리적 오류를 최대한 줄이는 것이 더 우선되고 중요합니다.** 다만, **사용자의 의도치 않은 입력으로 인해 런타임 에러가 발생할 것이라고 예상되는 경우에 대하여는 런타임 에러 예외처리를 해주는 게 중요**합니다.

```java
try {
    // 예외가 발생할 가능성이 있는 코드
} catch (NullPointerException e) {
    // 예외 처리 코드
}
```

**3. Checked Exception vs Unchecked Exception**

예외를 모두 알 필요는 없지만, 대표적인 예외들 정도는 인지하고 있는 게 좋습니다. 다음은 대표적인 CHECKED EXCEPTION과 UNCHECKED EXCEPTION들을 정리한 표입니다.

|Exception|설명|종류|
|---|---|---|
|ArrayIndexOutOfBoundsException|배열의 접근 범위를 벗어난 경우|UnCheckedException|
|NegativeArraySizeException|배열의 크기를 음수로 지정한 경우|UnCheckedException|
|NullPointerException|객체를 선언만 하고 생성하지 않은 상태에서 접근한 경우|UnCheckedException|
|ClassCastException|Reference 타입의 잘못된 형변환시 발생|UnCheckedException|
|NumberFormatException|문자열로된 데이터를 Primitive로 변환시 잘못 변경하면 발생|UnCheckedException|
|IndexOutOfBoundsException|List에서 잘못된 index에 데이터를 추가한 경우|UnCheckedException|
|ArithmeticException|0으로 나눈 경우|UnCheckedException|
|IllegalArgumentException|메서드에 잘못된 인자를 전달한 경우|UnCheckedException|
|InterruptedException|쓰레드 수행중 중단 명려에 따른 오류|UnCheckedException|
|FileNotFoundException|지정한 경로에 파일이 없는 경우|CheckedException|
|IOException|데이터를 IO하는 중 발생하는 오류|CheckedException|
|SQLException|DB서버에서 데이터 처리 중 발생하는 오류|CheckedException|

Checked Exception과 Unchecked Exception의 가장 큰 차이는 '처리의 의무'에 있습니다. Checked Exception은 반드시 예외 처리를 해주어야 하지만, Unchecked Exception은 선택적입니다. 하지만 좋은 소프트웨어를 개발하기 위해서는 Unchecked Exception 역시 예외 상황을 적절하게 잘 조치해주어야 합니다.

