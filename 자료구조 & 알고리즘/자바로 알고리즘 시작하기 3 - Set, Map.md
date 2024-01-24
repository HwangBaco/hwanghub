`List`와 `Queue` 컬렉션에 대하여 궁금하다면 [이전 게시글](https://hwanghub.tistory.com/740)에서 공부해보자.

## Set

Set 컬렉션은 List 구조와 달리, 저장 순서를 유지하지 않는 자료구조이다. 즉, 선형 구조를 갖고 있지 않다. 객체를 중복해서 저장할 수 없다는 것이 가장 큰 특징이며, null은 하나까진 저장 가능하다.

```java
Set<E> hs = new HashSet<>();
Set<E> lhs = new LinkedHashSet<>();
Set<E> ts = new TreeSet<>();
```

가장 자주 사용되는 Set 구현 클래스는 `HashSet`이다. 이 외에도 `TreeSet`과 `LinkedHashSet`이 존재한다. 지금은 가장 많이 사용되는 HashSet을 중점으로 Set을 이해해볼 예정이라, 다른 Set 구현체에 대하여는 다음 설명만 드리고 넘어가겠다. 이것에 대하여 궁금한 사람은 한번 찾아보시면 재밌을 듯 하다. 
- `LinkedHashSet`은 순서라는 개념이 존재하지 않는 HashSet의 단점(?)을 극복한 자료구조라고 이해하면 된다. 따라서 순서를 저장해야 하면서 중복을 허용하지 않는 경우에 사용할 수 있다. 아쉽게도,,, 하나만 조회하는 기능은 없다.
- `TreeSet`은 내부적으로 TreeMap(Navigatable Map의 구현체)을 사용하며 이는 이진탐색트리의 단점을 개선한 Red-Black Tree라는 구조로 구현되어 있는 자료구조이다. 따라서 정렬/탐색에 유리한 자료구조를 사용하였기에 중복을 허용하지 않는 데이터 중에서 최대최소값을 찾을 때 사용한다.

> TreeMap class is a Red-Black tree based {@link [NavigableMap](https://docs.oracle.com/javase/7/docs/api/java/util/NavigableMap.html)} implementation. The map is sorted according to the {@linkplain Comparable natural ordering} of its keys, or by a {@link Comparator} provided at map creation time, depending on which constructor is used.

Set에서 지원하고 있는 메서드는 다음과 같다.

| 기능 | 메서드 | 설명 |
| :--- | :--- | :--- |
| CREATE | `boolean` add(E e) | - 주어진 객체 저장에 성공하면 `true`를 반환<br>- 중복 객체를 저장하려고 시도하면 `false`를 반환<br>*** 즉, 중복된 객체를 저장하려고 해도 저장이 안됨. |
| READ | `boolean` contains(E e) | 주어진 객체가 저장되어 있는지 탐색 |
|  | `boolean` isEmpty() | 컬렉션이 비어있는지 반환 |
|  | `iterator<E>` iterator() | 저장된 객체를 한 번씩 가져오는 Iterator 반환 |
|  | `int` size() | 저장되어 있는 전체 객체 수를 리턴 |
| DELETE | `void` clear() | 저장된 모든 객체를 삭제 |
|  | `boolean` remove(E e) | 주어진 객체를 삭제 |

```java
Set<E> hs = new HashSet<>();
```

제네릭 타입 파라미터 E에는 저장하려는 객체를 선언해야 한다. 즉, 제네릭에 익숙한 사람은 알겠지만, E에는 참조 타입만 가능하다. 따라서 `int`는 안되지만 `Integer`는 가능하며, `int[]`도 가능하다.

```java
Set<String> hs = new HashSet<>();

hs.add("java");  // ok
hs.add("hello"); // ok
hs.add("java");  // 저장X
```

### add

HashSet에 데이터를 저장하는 `add()` 메서드 코드를 까보자.

```java
class HashSet {
	// ...
	public boolean add(E e) {  
		return map.put(e, PRESENT)==null;  
	}
}
```

위 코드를 보면 알겠지만, HashSet은 내부적으로는 HashMap을 이용하여 해시값을 바탕으로 데이터를 저장하기 때문에 순서를 고려하지 않는 구조인 것을 바로 이해할 수 있다.

그럼 중복 검사는 어떻게 하는 걸까? 중복 검사는 hashCode와 `equals()` 메서드를 이용하여 검사한다. 
1. hashCode 리턴값이 다른가 `?` 다르면 다른 객체로 판별 `:` 같으면 equals() 메서드로 비교
2. equals() 리턴값이 다른가 `?` 다르면 다른 객체로 판별 `:` 같으면 동등 객체로 판단 (=저장 X)

위 두 가지 방식으로 비교하여 동등 검사를 하며, 이 메서드는 Object에서 지원하고 있으므로 이를 오버라이드 하는 객체가 `<E>` 타입 파라미터에 입력되면 그 객체의 오버라이드된 hashCode와 `equals()` 메서드를 이용하여 비교하게 된다. (HashSet 외에도 대부분의 경우에 "HashCode & Equals" 방식으로 중복 검사를 하게 되므로, 이 두 메서드를 재정의할 경우 자바에서는 하나만 재정의하기보다는 둘 다 같이 재정의하길 권장하고 있다.)


### 조회

Set에서는 `get()`과 같은 조회 메서드를 지원하지 않는다. 집합이라는 컬렉션은 애초에 중복 제외 처리 또는 중복 여부를 검사하기 위한 자료구조이기 때문에, 집합에서 특정 객체만을 조회하는 기능을 제공할 필요가 없다고 판단한 것 같다. (=만약 하나하나 조회가 자주 일어나야 할 경우에는 List 또는 Map이 적합할 것이다.)

그렇다면 Set에 한번 들어가면 내부 Elements 중 특정 element를 제거하려면 어떻게 해야 할까? Iterator를 사용하면 가능하다.

```java
Set<E> hs = new HashSet<>();
Iterator<E> it = hs.iterator();

while(it.hasNext()) {
	E e = it.next(); // 컬렉션에서 다음 객체를 가져온다.
	if (e.equals(target)) {
		iterator.remove(); // 컬렉션에서 해당 객체를 제거한다.
	}
}
```

위와 같이 `iterator`를 이용하면 컬렉션 순회가 가능하며, 이는 컬렉션 프레임워크가 `Iterator` 인터페이스를 상속하고 있으므로 모든 컬렉션에서 지원된다. C++을 했던 사람은 iterator라는 개념이 익숙할텐데, C++에서는 이를 포인터로 구현하지만 자바에서는 포인터라는 개념이 존재하는 건 아니다. 다만, 내부적으로 마치 포인터처럼 사용하여 컬렉션을 순회하고 조작할 수 있도록 구현되어 있다. 

참고로, iterator를 사용하여 순회한 위 로지은 다음 로직과 동일하다.

```java
for (E e : hs) {
	if (e.equals(target)) {
		hs.remove(e);
	}
}
```

컬렉션 순회에 대하여 for loop를 저렇게 사용하는 방식이 내부적으로 iterator를 사용하는 방식이다.

## Map

Map에서는 Key와 Value가 한 쌍으로 구성되는 Entry라는 객체를 모아모아 저장하며 자료를 관리한다. Key는 중복될 수 없고, Value는 중복이 가능하다. 이러한 특징을 활용하여 HashSet에서도 HashMap을 이용하고 있는 것을 알 수 있었다. Map에서는 기존에 저장된 key에 다른 value로 구성된 entry가 입력되면 overwrap되어 저장된다.

Map 인터페이스를 구현하는 주요 클래스는 `HashMap`, `LinkedHashMap`, `TreeMap`이 있다. 그 외에 `HashTable`과 이를 상속하여 `<String, String>`으로 가볍게 사용하도록 구현된 `Properties`라는 클래스도 있는데, 이를 우리가 코드 상에서 사용할 일은 (아마) 없을 것이다. `HashTable`은 과거 자바 초기에 Vector와 Stack과 같이 만들어진 자료구조이기 때문에 오늘날 사용할 이유가 없으며, (동시성 제어를 하고싶다면 ConcurrentHashMap을 사용할 것) Properties는 프로젝트 설정 파일로 사용하는 확장자 중 하나인 `.properties`라는 파일을 구성할 때 사용한다고 한다. 즉, 아예 볼일 없는 건 아니지만 우리가 코드 레벨에서까지 사용할 일은 없다.

Map 인터페이스에서 지원하는 메서드는 다음과 같다.

| 기능 | 메서드 | 설명 |
| :--- | :--- | :--- |
| CREATE | `V` put(K key, V value) | key와 value를 추가하고, value를 리턴 |
| READ | `boolean` containsKey(K key) | 주어진 키가 있는지 true / false 반환 |
|  | `boolean` containsValue(V value) | 주어진 값이 있는지 true / false 반환 |
|  | `Set<Map.Entry<K, V>>` entrySet() | 키, 값 쌍으로 구성된 map의 모든 entry객체를 set에 담아서 반환 |
|  | `V` get(K key) | 키에 매핑되는 값을 반환 |
|  | `boolean` isEmpty() | 컬렉션이 비어있는지 true / false |
|  | `Set<K>` keySet() | 모든 key를 Set에 담아서 반환 |
|  | `Collection<V>` values() | 모든 value를 Collection에 담아서 반환 |
|  | `int` size() | map에 담긴 key의 개수를 리턴 |
| DELETE | `void` clear() | 모든 map의 entry를 삭제 |
|  | V remove(K key) | 대응되는 키의 entry 삭제하고 value 반환 |

### HashMap

이번에도 대표적으로 사용되는 Map 구조인 HashMap을 기준으로 Map을 이해해보자. key의 동등 비교 연산은 set에서 이미 다뤘지만, "HashCode & Equals" 방식으로 구성된다. 이는 Set에서도 내부적으로 Map을 사용하고 있기 때문에 똑같은 게 자명하다.

```java
Map<K, V> hm = new HashMap<>();
```

위는 hashMap을 선언하는 하나의 예시이다. K와 V 타입 파라미터에는 당연히 참조 타입이 입력되어야 한다. Set과 겹치는 느낌이 많아서 간단히 넘어가겠다.

map을 순회할 때에는 아래와 같이 한다. 이번에는 출력을 예시로 들어보겠다.

```java
Map<String, Integer> hm = new HashMap<>();

// key 순회
Set<String> keySet = hm.keySet();
Iterator<String> it = keySet.iterator();
while(it.hasNext()) {
	String k = it.next();
	Integer v = hm.get(k);
	System.out.println(k + " : " + v); // k : v
	it.remove();
}

// entry 순회
Set<Entry<String, Integer>> entrySet = hm.entrySet();
Iterator<Entry<String, Integer>> it = entrySet.iterator();
while(it.hasNext()) {
	Entry<String, Integer> entry = it.next();
	String k = entry.getKey();
	Integer v = entry.getValue();
	System.out.println(k + " : " + v); // k : v
	hm.remove(k);
}

for (Map.Entry<String, Integer> e : hm.entrySet()) {
	String k = e.getKey();
	Integer v = e.getValue();
	System.out.println(k + " : " + v); // k : v
	hm.remove(k);
}
```

지우는 방식은 `iterator.remove()`를 이용할 수도 있고, `remove(key)`를 사용할 수도 있다.

## 마무리

이번까지 해서 기본적인 컬렉션에 대하여 다뤄봤다. 나중에 TreeSet과 TreeMap에 대하여도 다룰 기회가 있다면 한번 다뤄보겠다.