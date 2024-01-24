자바는 객체를 파일에 저장하거나 네트워크를 통해 전송하기 위해 문자열과 같은 연속적인 데이터(byte stream; 자바는 기본적으로 byte stream으로 입출력 관리)로 변환하는 과정을 처리합니다. 이를 직렬화라 합니다. 당연히 직렬화된 데이터(byte stream)를 다시 객체화하는 것을 역 직렬화라고 합니다.

직렬화가 되기 위한 조건은 다음과 같습니다.
- 클래스와 그 모든 멤버가 Serializable 인터페이스를 구현해야 함
- 직렬화에서 제외하려는 멤버는 transient를 선언해야 함

```java
class Member implements Serializable /* 직렬화 필수 조건 */{
	private Long id;
	private String name;
	private Integer age;

	private transient String ssn; // 직렬화 제외
	private LoginInfo loginInfo; // 직렬화 필요
}
```

## SerialVersionUID가 뭔가요?

직렬화 과정에서 발생할 수 있는 위험중에선 ` "does not define a 'serialVersionUID"` 과 같은 경우가 있습니다. 그렇다면 `serialVersionUID`가 뭘까요?

`serialVersionUID`는 "클래스의 변경 여부를 파악하기 위한 유니크 키" 입니다. 직렬화되는 객체에 UID가 설정되지 않았을 경우에는 컴파일러가 이 UID를 자동으로 생성하며, 객체의 필드(멤버 변수)가 변경되면 컴파일 시마다 이 UID를 변경합니다.

이렇게 클래스가 수정되면 serialVersionUID가 변경되는데, 이 경우 직렬화 할 때의 UID와 역 직렬화 할 때의 UID가 다를 경우에는 자바에서 역직렬화 과정에서 해당 UID를 어떤 객체로 역직렬화할지 이해하지 못하므로 `InvalidClassException`이 발생합니다. 이를 방지하고자 한다면, 변경해야 하는 클래스에 대하여 명시적으로 serialVersionUID를 설정하여 일관된 serialVersionUID를 유지하게 만들 수 있습니다.

```java
Caused by: java.io.InvalidClassException : stream classdesc serialVersionUID = 6823753533812448682, local class serialVersionUID = 8546197362506746118
```

Java에서 클래스의 직렬화 버전을 명시적으로 선언하는 방법은 `serialVersionUID`라는 이름의 `static final long` 필드를 선언하는 것입니다. 참고로, 이는 각 클래스 별로 버전 ID를 명시하는 개념이라서 모든 직렬화 대상 클래스에 `1L`로 입혀줘도 문제가 되지 않습니다. 이는 갑자기 역직렬화 문제로 뻑 나는걸 막기 위한다는 고결한 가치를 위해 다들 습관적으로 한다고 합니다.

```java
private static final long serialVersionUID = 1L;
```

## 두 눈으로 확인해보기

아래는 직렬화를 하는 간단한 코드입니다. 백엔드 서버를 구현하는 과정에서는 자바 객체와 json간 직렬화/역직렬화 라이브러리로 사용되는 Jackson에서는 내부적으로 java reflection API를 가지고 구현되어 있다고 하는데, 예시 코드에서는 ObjectOutputStream, ObjectInputStream에서 `implements Serializable`된 객체만 내부 로직을 통해 바이트 코드로 구성하여 파일로 저장해줍니다.

```java
class Person implements Serializable {
	private Long id;
	private transient String pass; // 민감한 데이터 직렬화 제외
	private Address addr; // 멤버 객체 -> Serializable 구현 필요
	
	public Person(Long id, String pass, String zipCode, String city) {
		this.id = id;
		this.pass = pass;
		this.addr = new Address(zipCode, city);
	}

	class Address implements Serializable {
		private String zipCode;
		private String city;

		public Address(String zipCode, String city) {
			this.zipCode = zipCode;
			this.city = city;
		}
	}
}
```

Person이라는 클래스 인스턴스를 직렬화하여 바탕화면에 저장한 뒤 다시 코드로 불러와서 객체 정보를 출력해 보겠습니다. 직렬화 / 역직렬화는 `implements Serializable`를 해줌으로써 자동으로 처리됩니다.

(아래에서는 절대 경로를 사용했는데, 사실 이는 협업 과정을 고려했을 때에는 권장하는 방식은 아닙니다. 그냥 직관적으로 이해하기 위함이니 넘어가주시길 바랍니다. :)

```java
public class SerializationTest {  
    private static void write(Person person, File file) {  
        try(ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream(file))) {  
            oos.writeObject(person);  
            System.out.println("저장 완료.");  
        } catch (FileNotFoundException e) {  
            throw new RuntimeException(e);  
        } catch (IOException e) {  
            throw new RuntimeException(e);  
        }  
    }  
  
    private static void read(File file) {  
        try (ObjectInputStream ois = new ObjectInputStream(new FileInputStream(file))) {  
            Object readed = ois.readObject();  
            if (readed != null && readed instanceof Person) {  
                Person casted = (Person) readed;  
                System.out.println("로딩 완료 -> " + casted);  
            }  
        } catch (FileNotFoundException e) {  
            throw new RuntimeException(e);  
        } catch (IOException e) {  
            throw new RuntimeException(e);  
        } catch (ClassNotFoundException e) {  
            throw new RuntimeException(e);  
        }  
    }  
  
    public static void main(String[] args) {  
        File file = new File("C:\\Users\\Username\\Desktop\\objPerson.dat");  
        Person person = new Person(1L, "pass1234", "123-456", "seoul");
        write(person, file); // 저장 완료  
        read(file);  // 로딩 완료 : id : 1, pass : null, zipCode : 123-456, city : seoul    
	}  
}
```

위에서 볼 수 있듯, `transient`를 먹여뒀던 pass 값은 null로 되어있고, 나머지는 잘 조회되는 걸 확인할 수 있습니다. 심심하니 직렬화된 데이터 `objPerson.dat`도 들여다 봐볼까요?

```json
// objPerson.dat
¬í^@^Esr^@^Spackage.ì<9e><90>ë°<94>.PersonÇË^U¹ZC^N5^B^@^BL^@^Daddrt^@^]Lssafy/ì<9e><90>ë°<94>/Person$Address;L^@^Bidt^@^PLjava/lang/Long;xpsr^@^[ssafy.ì<9e><90>ë°<94>.Person$Addressä¨,^F¨?"(^B^@^CL^@^Dcityt^@^RLjava/lang/String;L^@^Fthis$0t^@^ULssafy/ì<9e><90>ë°<94>/Person;L^@^GzipCodeq^@~^@^Expt^@^Eseoulq^@~^@^Ct^@^G123-456sr^@^Njava.lang.Long;<8b>ä<90>Ì<8f>#ß^B^@^AJ^@^Evaluexr^@^Pjava.lang.Number<86>¬<95>^]^K<94>à<8b>^B^@^@xp^@^@^@^@^@^@^@^A
```

대애애애충 앞의 `¬í^@^Esr^@^S` 이 부분이 version id라고 한다고 하네요. 그 외에 다른 코드에 "Person 클래스의 정보다"라는 내용이 적혀 있는 거라서 `1L`로만 처리해줘도 무방하다고 합니다.

그렇다면 일부러 한번 클래스에 수정을 가해서 예외를 터뜨려 보겠습니다.

```java
class Person implements Serializable {  
	private static final long serialVersionUID = 1L;
	
    private Long id;  
    private transient String pass;
    private Address addr;
  
    public void noMeaningMethod() {  // 추가된 메서드
        System.out.println("no mean");  
    }  
    
	// ... 이하 동일
}
```

우리는 역직렬화나 직렬화 과정에서 궁금한 건 보통 멤버 변수의 데이터들일 겁니다. 하지만 위와 같이 멤버 변수에 대한 수정 없이 메서드 하나만 추가해줘도 `java.io.InvalidClassException`이 발생합니다. (다행히도(?) 주석은 추가되어도 문제 없습니다.) 따라서 이런 런타임 에러를 방지하기 위해 위에서 말했던 '중요한 습관'을 실천해 보겠습니다.

```java
class Person implements Serializable {  
	private static final long serialVersionUID = 1L; // 추가!
	
    private Long id;  
    private transient String pass;
    private Address addr;
    private int phoneNumber;
    
	// ... 이하 동일
}
```

이렇게 하고 기존 직렬화되어있던 파일을 역직렬화를 시도해도 문제가 없는지 확인해 봤습니다. 출력은 어떻게 나올까요?

```java
public static void main(String[] args) {  
	File file = new File("C:\\Users\\Username\\Desktop\\objPerson.dat");  
	Person person = new Person(1L, "pass1234", "123-456", "seoul");  
//        write(person, file); // 저장 완료  
	read(file);  // 로딩 완료 -> id : 1, pass : null, zipCode : 123-456, city : seoul, phoneNumber : 0    
}
```

당연히도(?) 예외가 터지지 않고 역직렬화가 정상적으로 이루어 졌으며, `transient`처럼 없던 데이터에 대하여는 `int`는 객체 생성시 기본값이 들어가는 것과 같이 0으로 들어온 것을 확인할 수 있습니다. (아까 `String pass`에 대하여 null이 된 것도 같은 원리) [혹시 이게 지금 무슨 말인지 모른다면 여기로 오세요](https://hwanghub.tistory.com/763)

## 마무리

- 직렬화와 역직렬화 간에 `serialVersionUID`는 클래스 데이터 버전 관리를 위해 관리되는 값이다.
- 의도치 않은 런타임 에러로 뻑이 나는 걸 방지하기 위해 이를 명시해주는 것을 습관화하자.
- 동작을 이해하고 있어야 디버깅이 빨라진다!

감사합니다.