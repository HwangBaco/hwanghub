자바에서 배열은 참조 변수로 취급되며, 이는 메모리에 저장된 객체를 가리키는 주소 값을 갖는 변수를 의미합니다. 이러한 참조 변수의 특성 때문에 배열을 출력하면 대부분의 경우 해당 변수가 가리키는 메모리 주소가 출력됩니다. 하지만 char\[\] 배열의 경우에는 예외적으로 배열 안에 있는 문자들이 직접 출력됩니다. 이에 대해 좀 더 자세히 알아보도록 하겠습니다.

## 일반 배열 출력

자바에서 int\[\], double\[\] 등의 배열을 선언하고 출력하려고 하면, 배열 변수가 가리키는 메모리 주소가 출력됩니다. 이는 배열이 참조 변수로 취급되기 때문입니다.

```
int[] arr = {1, 2, 3, 4, 5};  
System.out.println(arr); // [I@7ef20235  
System.out.println(arr.toString()); // [I@7ef20235  
```

위의 코드에서 `arr`는 참조 변수로, 생성된 배열 객체의 주소 값을 가리키게 됩니다. 따라서 `println()` 메서드로 출력할 경우 주소 값(엄격히 말하면 주소값과 매핑되는 해시코드)인 `[I@7ef20235`가 출력됩니다. 이는 배열의 타입과 해시코드를 나타내며, 이러한 형태의 출력은 대부분의 배열에서 공통적으로 확인할 수 있습니다.

## char\[\] 배열 출력

하지만 char\[\] 배열은 다른 배열과는 달리 내부적으로 `BufferWriter`를 이용하여 각 character를 조합하여 한번에 출력해줄 수 있도록 `println()` 메서드가 오버로딩되어 있습니다.

```
String org = "HELLO";  
char[] charArray = org.toCharArray();  
System.out.println(charArray); // HELLO  
System.out.println(charArray.toString()); // [C@7ef20235
```

위의 코드에서 `charArray`를 `println()` 메서드로 출력하면, 배열 안에 있는 문자들이 직접 출력됩니다. 이는 `PrintStream` 클래스에서 `char[]`를 입력으로 받는 `println()` 메서드가 오버로딩되어 있기 때문입니다. 이 메서드는 내부적으로 `BufferWriter`를 이용하여 배열 내의 모든 문자를 조합하여 문자열로 변환한 뒤 출력해줍니다. 아래는 println코드의 일부분입니다.

```
public void println(char[] x) {  
    if (...) {  
        writeln(x);  
    } 
    // ...
}

private void writeln(char[] buf) {  
    try { 
        implWriteln(buf);
    } 
    // ...
}  

private void implWriteln(char[] buf) throws IOException { 
    BufferedWriter textOut = new BufferedWriter(...);
    ensureOpen();  
    textOut.write(buf);  
    textOut.newLine();  
    textOut.flushBuffer();  
    // ...
}
```

결론적으로, 자바에서 배열을 출력하려고 하면 대부분의 경우 참조 변수가 가리키는 메모리 주소가 출력되지만, char\[\] 배열은 예외적으로 배열 안에 있는 문자들이 직접 출력됩니다. 이는 자바의 `PrintStream` 클래스에 내장된 `println()` 메서드의 오버로딩 덕분입니다.

### 그냥... 신기해서 써봤습니다.

별건 아니고, 사실 `char[]`을 애초에 잘 쓰지도 않지만, 신기해서 한번 정리해 봤습니다 :)

(생각해보면 자바에서 String 객체 데이터를 char\[\]로 관리하고 있으니 char\[\]가 문자열로 출력되는게 자명하긴 하네요... 글을 쓰다가 아차 싶은 순간이 이따금씩 옵니다.)