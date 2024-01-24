자바에서는 기본형 변수외에 참조형 변수를 활용할 때 'pass by value'라는 용어를 사용하곤 합니다. 그러나 이 'pass by value'는 기본형 변수를 전달할 때의 'pass by value'와는 다소 차이가 있습니다.

기본형 변수를 'pass by value'로 전달할 때는 변수의 실제 값이 복사되어 전달됩니다. 그러나 참조형 변수를 'pass by value'로 전달할 때는 변수가 참조하고 있는 객체의 주소(참조 값)가 복사되어 전달됩니다. 즉, 이 경우 'value'는 참조 '값'이며, 이 참조 값이 복사되어 전달되는 것이기 때문에 여전히 'pass by value'라는 용어를 사용하는 것입니다.

그렇다면 이것이 'call by reference'와 어떻게 다른 것일까요? 'call by reference'는 포인터를 이용하여 원래 변수 자체의 참조(즉, 변수의 주소)가 전달되는 것입니다. 따라서 'call by reference' 방식에서는 메모리 주소를 가리키는 포인터를 활용하여 함수 내부에서 인자를 통해 원래 변수 자체를 직접 변경할 수 있습니다. 이에 반해, 자바의 'pass by value'에서는 원래 참조형 변수 자체를 변경할 수 없습니다. 함수 내부에서 인자를 통해 원래 참조형 변수가 참조하고 있던 객체는 변경할 수 있지만, 그 변수가 참조하고 있는 객체 자체를 다른 객체로 변경하는 것은 불가능합니다. 이는 인자로 전달받은 것이 원래 참조형 변수의 참조가 아니라 참조 값의 복사본이기 때문입니다.

다음은 이 차이를 보여주는 자바 코드 예제입니다.

```
static void update(StringBuffer buffer) {  
    buffer.append(" world");  
    buffer = new StringBuffer("Hello");  
    buffer.append(" Java");  
    System.out.println("inside : " + buffer); // 출력값 : "inside : Hello Java"
}  

public static void main(String[] args) {  
    StringBuffer buffer = new StringBuffer("Hello");  
    update(buffer);  
    System.out.println("outside : " + buffer);  // 출력값: "outside : Hello world"  
}
```

위 예제에서 `update` 함수 내에서 `buffer`를 새로운 `StringBuffer` 객체로 변경하려고 시도하였지만, `main` 함수의 `buffer` 값은 그대로 "Hello world"입니다. 이는 `update` 함수에서 `buffer`의 참조 값을 변경하더라도 그것이 원래 `buffer` 변수의 참조 값을 변경하지 못하기 때문입니다. 그러나 `buffer`가 참조하고 있는 원래 `StringBuffer` 객체에 " world"를 추가하는 것은 가능했습니다. 왜냐하면 그것은 `buffer`의 참조 값(즉, `StringBuffer` 객체의 주소)을 통해 수행되었기 때문입니다.

이처럼 자바 참조형 변수의 'pass by value'와 'call by reference'는 비슷해 보이지만 중요한 차이를 가지고 있습니다. 자바에서는 포인터를 개발자에게 노출시키지 않기 때문에 자바의 모든 변수는 `pass by value`방식으로 동작되고 있음을 잘 이해해야 합니다.