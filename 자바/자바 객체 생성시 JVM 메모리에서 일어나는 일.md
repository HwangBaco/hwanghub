> 이 글에는 사진이 많아 글이 깨집니다. [제 블로그](https://hwanghub.tistory.com/756)에서 확인 바랍니다.

JVM 메모리 영역(JVM runtime data area)은 meta space(method area), stack, heap, pc register, native method stack 다섯가지로 구분되어 구성된다. 자바는 멀티 스레드를 지원하는데, JVM 내에서 모든 쓰레드가 공유하는 메모리 영역(heap, meta-space)이 존재하고, thread 별로 구분되어 생성되는 영역이 존재한다.

![](https://velog.velcdn.com/images/indongcha/post/6e6fc014-5a14-4e60-bbaa-d3a7fba2b32f/image.png)

개념적으로만 보면 복잡하니 자바에서 main 메서드를 생성하는 과정에서 JVM에 일어나는 일을 보면서 해당 구조를 이해해보자.

```
public class Main {  
    public static void main(String[] args) {  
        Customer customer = new Customer();  
        customer.name = "KIM";  
        customer.age = 10;  
        customer.address = "Seoul";  
    }  
}

public class Customer {
    String name;
    int age;
    String address;

    public void sayHi() {...}
}
```

위 두 개의 자바 클래스 파일만 있는 프로젝트를 실행해 본다고 가정하자.

```
public class Main {
    public static void main(...) {}
}
```

자바는 동적 로딩을 하는 언어이기 때문에 런타임에서 클래스를 로드한다. 따라서 컴파일 단계에서 자바 바이트 코드를 생성한 뒤에, JVM에서 인터프리터를 통해 line by line으로 실행하게 된다. 실행 전에는 JVM runtime data area에는 아무것도 없다. 하지만 main 메서드를 찾아서 실행해야 하므로 이를 갖는 클래스를 로드하면서 시작된다. 이 때, main 메서드도 static 으로 선언되어 있으므로 meta-space에 같이 바로 로드된다.

[##_Image|kage@cYo4Gg/btsDCTBG0uo/q7vwkjRH5LGmklaFkeL1jK/img.png|CDM|1.3|{"originWidth":1034,"originHeight":553,"style":"alignCenter"}_##]

그리고 나서 다음 줄을 보자.

```
public static void main(String[] args) {}
```

위 메서드 코드 실행을 위한 main thread의 stack에 main 메서드를 위한 스택 프레임이 할당된다. 여기서 파라미터인 `String[] args`도 로컬 변수이므로 main 메서드의 스택 프레임에 같이 담기게 된다.

[##_Image|kage@NUP7g/btsDAwmRKzk/kmakSTMF5kbqJKPSKBDDTK/img.png|CDM|1.3|{"originWidth":1040,"originHeight":565,"style":"alignCenter"}_##]

이제 중요한 객체 생성 부분이다.

```
Customer customer = new Customer();  
```

우선 이해를 더 쉽게 하기 위해 위 코드를 아래처럼 분리하겠다.

```
Customer customer;
customer = new Customer();
```

위 과정 순서대로 코드가 진행될 것이다.

우선 main() 메서드의 스택 프레임에 Customer라는 레퍼런스 타입 데이터를 담아둘 `customer`라는 main 메서드의 로컬 변수를 스택 프레임에 할당해줘야 한다. (여담이지만, customer는 힙에 저장되는 객체 데이터의 메모리 번지의 해시코드를 담을 예정이고, 이는 기본 정수형인 int 만큼의 크기를 지니므로 4 byte만큼 할당된다.)

[##_Image|kage@HaHQw/btsDASpIkAx/izSk1F9vqkD5HIHZSZnBqk/img.png|CDM|1.3|{"originWidth":1043,"originHeight":565,"style":"alignCenter"}_##]

이제 `new` 연산을 해야 한다.

```
customer = new Customer();
```

생성자를 수행하는 `new`라는 키워드는 마치 메서드를 생성하는 것과 같이 객체 생성을 위한 스택 프레임이 스택에 할당된다.

[##_Image|kage@JtXjI/btsDDdfx0TQ/Sx6mI0rSZ3yT4Xx5k1AFoK/img.png|CDM|1.3|{"originWidth":1046,"originHeight":575,"style":"alignCenter"}_##]

그리고 기본 생성자가 아니라면 일반적으로 해당 로컬 변수들을 위해 new 스택프레임에 parameters를 위한 공간이 할당될 것이다. 가령 예를 들어,

```
customer = new Customer(1, "이름");
```

이라고 가정하면, `1`과 "이름"을 저장하기 위한 공간이 할당될 것이다. 그리고 "이름"은 문자열이므로 참조 변수라서 Heap에 "이름"이라는 객체가 생성되고 이를 가리키는 값이 new 스택프레임에 담길 것이다.

[##_Image|kage@bCRrUP/btsDAREjWsH/QgUcwKO3nhkoajy6ewrmh0/img.png|CDM|1.3|{"originWidth":1035,"originHeight":571,"style":"alignCenter"}_##]

그리고 new 키워드를 통해 객체를 생성하기 위해 Customer 라는 class 데이터를 meta-space에 동적으로 로딩한다. 이 때 만약 클래스에 정적 데이터나 상수가 있다면 그 데이터도 meta-space에 로드된다.

[##_Image|kage@bc07Du/btsDCSQjUvK/zHbN8MW9UWDYtD02aHfzNK/img.png|CDM|1.3|{"originWidth":1038,"originHeight":555,"style":"alignCenter"}_##]

그리고 나서 customer 객체 데이터를 담을 공간을 heap에 할당하고, meta-space에 있는 해당 클래스 데이터 번지를 heap에 할당된 메모리 공간에 우선 담아둔 뒤, 객체를 구성하는 필드값들을 초기화한다. 이 때, 나는 기본생성자를 통해 객체를 초기화하였으므로, int값인 age는 0으로 초기화되며, String 값들은 레퍼런스 타입이기 때문에 heap에 별도로 생성하여 null로 초기화된다. 그리고 스택 프레임에는 this라는 키워드를 담아둘 공간을 할당한 뒤, 힙에 객체 데이터를 할당한 해당 번지를 new 스택프레임의 this에 저장해둔다. 그리고 그 값을 `customer` 변수에 저장한 뒤 new 스택프레임은 삭제된다.

[##_Image|kage@bdmf9I/btsDyBP7JOg/etsBlFHWAweMaVd9v8MZfK/img.png|CDM|1.3|{"originWidth":1041,"originHeight":558,"style":"alignCenter"}_##]

이제 다음 코드는 그냥 데이터를 저장하는 코드이다.

```
customer.name = "KIM";  
customer.age = 10;  
customer.address = "Seoul";  
```

인스턴스에 `.`를 사용하면 해당 힙 영역에 접근하여 데이터를 조회하고, 수정할 수 있다. 이를 통해 String 객체에 값을 대입하거나, age 값을 업데이트 하게 된다.

[##_Image|kage@d5stav/btsDDa4e4jZ/X12nXM3WKPIgbz0T2fXUCk/img.png|CDM|1.3|{"originWidth":1035,"originHeight":559,"style":"alignCenter"}_##]

이렇게 코드가 끝난다.

main thread만 본 거라 혼란스러울 수 있지만, stack은 쓰레드 별로 개별적으로 가지고 있다는 것도 잘 기억해야 한다. 여기서 편의상 자바가 문자열을 관리하는 특별한 방법에 대하여는 다루지 않았다. 해당 내용까지 참고하려면 아래 포스팅을 봐보시길 바란다.

 [자바의 String 객체 관리 비법

문자열은 자바에서 유일하게 new 키워드를 사용하지 않고 생성할 수 있는 객체이다. ""를 리터럴이라고 하는데, "" 리터럴로 선언해두면 컴파일 단계에서 문자열 객체를 생성할 수 있도록 자바 내

hwanghub.tistory.com](https://hwanghub.tistory.com/754)