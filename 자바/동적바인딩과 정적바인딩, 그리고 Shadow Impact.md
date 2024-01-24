참조 변수(오브젝트) 다형성은 많이 다뤄지지만, 사실 기본형 데이터들도 다형성을 가집니다.

`byte` => `short` / `char` => `int` => `long` => `float` => `double`

우리는 이걸 형변환이라고 알고 있죠. 하나의 타입이 여러 타입을 품을 수 있는 성질이 다형성이며, 이는 객체 간의 상속 관계 뿐만 아니라, 이와 같은 기본형의 형 변환이라는 현상도 만들어 냈습니다. (근데 중요하진 않으니 이쯤하고 넘어가죠.)

오늘 다룰 내용은 객체지향 원칙 A P.I.E 중 다형성에 대한 내용입니다.

## 다형성, 눈으로 확인하기

우리 모두 잘 알다시피, 참조 변수에서의 다형성은 상속 관계에서 많이 다뤄집니다. 이는 클래스 간 상속이나 인터페이스와 클래스 간의 관계에서 이용됩니다. 아래 예시를 통해 더 자세히 다뤄보죠.

```
public static class Hello {  
    public String s = "hello";  
    public String s2 = "hello2";  
    public void instancePrint() {  
        System.out.println("instance hello");  
    }  
    public static void staticPrint() {  
        System.out.println("static hello");  
    }  
}  

public static class World extends Hello {  
    public String s = "world";  
    public String w2 = "world2";  

    @Override  
    public void instancePrint() {  
        System.out.println("instance world");  
    }  
    @Override
    public static void staticPrint() {  
        System.out.println("static world");  
    }  
}
```

그다지 창의적이진 않지만 단순한 클래스 선언입니다. 보시면 아시겠지만, **Hello라는 부모 클래스가 있고, 이를 상속하는 World라는 클래스가 존재**합니다. 각각 `s`라는 멤버 변수를 동일한 이름으로 갖고 있고, 각각 서로 다른 이름의 `s2`, `w2`라는 멤버 변수를 가집니다.

그리고 `instancePrint()`라는 인스턴스 메서드가 있고, `staticPrint()`라는 스태틱 메서드가 있네요. 이제 상황 파악이 끝났으니 한번 코드를 통해 테스트하면서 다형성을 느껴봅시다.

```
public static void main(String[] args) {  
    // 객체 선언
    Hello hello = new Hello();  
    Hello world = new World();  
    var realWorld = new World(); // java 10부터 지원되는 타입 추론 로컬변수(컴파일시 추론)
    World realRealWorld = new World();  

    ...
}  
```

위 상황에서 볼 떄, `hello`는 Hello 클래스의 객체, `world`는 World 클래스의 객체, `realWorld`는 World 클래스의 객체, `realRealWorld`는 World 클래스의 객체임을 알 수 있습니다.

```
// 출력으로 확인하는 다형성
System.out.println(hello.s); // output : hello  
System.out.println(world.s); // output : hello  
```

이제 멤버변수 접근으로 한번 살펴 보겠습니다. `hello.s`가 "hello"인 것에는 이견이 없을 것입니다. 하지만 `world.s`도 "hello"인 것은 유념해야 합니다. 즉, **자바는 멤버 변수에 대하여는 레퍼런스 타입을 기준으로 해석**합니다. 사실 `world` 변수는 참조 변수의 타입만 Hello일 뿐, 실제 힙에 올라가 있는 건 World 클래스의 객체이잖아요? 그렇지만 자바는 컴파일 타임에서 참조 변수의 타입을 기준으로 데이터 접근을 구성하게 됩니다. 따라서 힙에 해당 값이 있음에도 접근할 수 없다고 하여 이를 "Shadow Impact"라고 말합니다.

```
System.out.println(hello.s2); // output : hello2  
System.out.println(world.w2); // "error!"  
System.out.println(realWorld.w2); // output : world2  

World worldCopy = (World) world;  
System.out.println(worldCopy.w2); // output : "world2"

World helloCopy = (World) hello;  
System.out.println(helloCopy.w2); // "error!"
```

다음입니다. `hello.s2`가 "hello2"인 것도 자명하죠. 그런데 위에서 다룬 것과 같이 **`world` 변수는 Hello 타입으로 선언되어 있으므로 World 클래스의 객체가 갖고 있을 멤버 변수인 `w2`에 접근이 되지 않아(shadow impact) error가 나는 것을 볼 수 있습니다.** 하지만 var를 통해 World 로 타입이 추론된 `realWorld`는 원활하게 `w2`멤버변수에 접근이 되는 걸 알 수 있습니다. 그리고 주의해야 하는 행위지만, 논리적으로 World 클래스의 객체임이 명확한 `world`변수를 World로 다운캐스팅하면 `w2`가 접근되는 걸 알 수 있습니다. 물론, Hello 타입인 게 명확한 `hello`변수는 다운캐스팅한 뒤 `w2`에 접근하려고 하면 없는 데이터이므로 Exception이 뜹니다.

```
Hello.staticPrint();    // output : static hello  
World.staticPrint();    // output : static world  

public static class World extends Hello {  
    // ...

    @Override // error
    public static void staticPrint() {  
        System.out.println("static world");  
    }  

    // ...
}
```

이제 static method에 대하여 다뤄보겠습니다. **static method도 (자명하게도) 멤버 변수처럼 컴파일 타임에 메모리에 올라가기 때문에 override가 불가능합니다.** 따라서 메타스페이스에 있는 클래스 데이터에서 바로 메서드 정보를 뽑아냅니다. 이렇게 이해하면 위와 같은 출력은 자명하다고 할 수 있습니다.

```
hello.instancePrint();  // output : instance hello  
world.instancePrint();  // output : instance world  
```

그리고 instance method를 보겠습니다. 여기서부터는 override한 결과가 의도대로 잘 보입니다. **인스턴스 메서드는 기본적으로 런타임에서 dynamic하게 힙에서 해당 객체를 확인한 뒤에 메타스페이스의 메서드 텍스트에 접근하기 때문에(동적 바인딩) 의도한대로 재정의한 메서드를 사용할 수 있습니다.** 이렇게 업캐스팅된 변수에서 메서드를 사용하더라도 런타임에서 동적으로 해당 객체의 메서드를 사용하는걸 **virtual (method) invocation**이라고 합니다.

```
// 타입의 유연함으로 보는 다형성
Hello[] hellos = new Hello[100];  
hellos[0] = hello;  
hellos[1] = world;  
hellos[2] = realWorld;  
hellos[3] = realRealWorld;  

for (Hello h : hellos) {
    System.out.println(h.instancePrint());
    // instance hello
    // instance world
    // instance world
    // instance world
}
```

마지막으로 배열을 통해서 다형성을 이해해보겠습니다. 이는 **World 클래스가 Hello 클래스를 상속받았으므로, World 인스턴스도 Hello** **타입으로 취급(묵시적 형변환; 업캐스팅)**될 수 있기 때문입니다. 이렇게 다형성은 코드의 유연성을 높여줍니다.

이제 메서드 호출을 확인해보면, **모두 Hello 타입의 배열에 담긴 Hello h라는 변수에서 추출되는 instancePrint()라는 메서드이지만, 모두 의도한 대로 class에 따라 재정의된 메서드가 호출**되는 걸 확인할 수 있습니다. 이를 통해서 다형성이라는 성질을 활용하여 굳이 타입을 구분하지 않고 Hello 타입의 배열에 모두 담아두더라도, 인스턴스 메서드는 동적으로 바인딩되는 걸 확실하게 확인할 수 있습니다.

### 정적 바인딩과 동적 바인딩

다형성과 관련되어 반드시 알아야 하는 것이 바인딩이라는 개념입니다.

객체지향 프로그래밍에서 바인딩이란 메서드 호출을 올바른 인스턴스에 연결하는 과정을 말합니다. 이는 크게 두 가지 방식으로 이루어집니다.

-   정적 바인딩 : **컴파일 타임**에서 **참조 변수의 타입**에 따라 연결
    -   대상 : `멤버 변수`, `static field`, `static method`
-   동적 바인딩 : **런타임**에서 메서드 호출 시점에 **메모리의 실제 객체 타입**에 따라 연결
    -   대상 : `instance method`

|   | 정적 바인딩 | 동적 바인딩 |
| --- | --- | --- |
| 수행 속도 | (상대적으로) 빠름 | (상대적으로) 느림 |
| 메모리 공간 효율 | (상대적으로) 높음 | (상대적으로) 낮음 |
| 객체지향적 |   | 다형성으로 효율적인 코드 재사용 가능 |

동적 바인딩은 런타임에서 메서드 호출시 힙 메모리를 스캔하여 해당 객체에 접근하여 해당 메서드를 사용하기 때문에 다형성을 적극적으로 활용할 수 있다. 하지만 static 한 메서드나 멤버 변수들은 컴파일 타임에 정적으로 이미 바인딩이 되어있기 떄문에 속도도 상대적으로 빠르고 메모리 효율도 최적화되어 있지만 다형성을 이용하는 것에는 제한된다는 것이 특징입니다.

(여담으로, 자바 클래스는 기본적으로 동적 바인딩으로 메모리에 로딩되므로, ".java"파일의 최상위 클래스는 static으로 선언이 불가합니다. 이는 동적 바인딩을 이해한 사람이라면 자명하다고 이해하실 수 있을 겁니다.)

## 동적 바인딩을 이용하여 Shadow Impact 해결하기

위 내용에서 주목해야 하는 점은, 메서드에 실제로 접근하게 되면 동적 바인딩으로 접근하기 때문에 다형성을 이용하여 상위 클래스 타입 변수로 받거나 하더라도 자식 클래스 메서드를 호출하는 것에 문제가 되지 않는데, 멤버 변수는 static도 아닌데도 상위 클래스 타입 변수로는 접근이 안됩니다. (진짜 간단하게 짠) 코드로 보겠습니다.

```
public class Parent {
    int time = 1;

    public void getTime() {
        System.out.println(time);
    }
}

public class Child extends Parent {
    int time = 1000;
    int health = 1000;
    
    public void getTime() {
        System.out.println(time);
    }
    public void getHealth() {
    	System.out.println(health);
    }

}

public static void main(String[] args) {
    Parent person = new Child();
    System.out.println(person.time); // output : 1
    person.getTime(); // output : 1000
    person.getHealth(); // error !
}
```

위 코드를 보면 Parent person 변수로 Child 클래스 인스턴스를 받는 것에는 문제가 없다는 건 위 글을 통해 누구나 이해하실 수 있을 겁니다(?). 주의할 점은, 위에서 다룬 것과 같이 person 변수의 실제 인스턴스는 Child 클래스의 객체라 할지라도, Parent 타입으로 변수를 선언했기에 자식 클래스가 정의하고 있는 time 변수에는 접근이 안됩니다. 이를 Shadow Impact라고 합니다.

**자바에서의 변수를 통한 데이터 접근은 컴파일 타임에 이미 변수의 타입을 기준으로 저장해 둔 것을 기준으로 제공**하기 때문에 이와 같은 문제가 발생하게 됩니다. 분명 힙에는 해당 데이터가 있는데 public 이든 private이든 상관없이 접근이 안되기 때문에 "눈에 보이는데 닿지 않는 것이 마치 그림자 같다" 하여 Shadow Impact라고 부르고 있는 겁니다. 이를 이유로 Parent 타입의 참조변수인 person 변수를 통해서는 자식의 time 필드 값이나 자식 객체의 getHealth() 메서드 접근이 안되는 겁니다. 

그럼, 그냥 눈 뜨고 코 베이면 되는 걸까요? 아니죠. 자바에서 인스턴스 메서드 접근은 동적 바인딩을 이용하여 접근할 수 있도록 제공하고 있으니 이를 이용하여 Shadow Impact를 해결하면 됩니다. 실제로 예시 코드 상에서도 getTime() 메서드 오버라이딩을 통해서 얻은 값이 자식의 time 값인 걸 확인할 수 있었습니다. 이를 통해 **객체 변수를 통한 데이터 접근은 참조타입 기준이지만, 실제 메서드 수행시의 값은 동적으로 바인딩된다**는 걸 알 수 있습니다. 따라서 이를 이용하여 getter 메서드를 적절하게 설계하면 shadow impact를 최소화할 수 있습니다.

## 마무리

다형성은 스프링 프레임워크를 이용하여 개발을 하다 보면 정말 밥먹듯이 볼 정도로 유연한 구조를 가능하게 하는 중요한 성질입니다. 평소에 어느정도는 알고 있던 주제이지만, 그럼에도 한번은 제대로 짚고 넘어가고 싶어 정리해 봤습니다. 문제가 있다면 댓글로 제보 부탁드립니다.