
### Lettuce
Lettuce(레디스 자바 클라이언트) 적용 ; 일종의 자바 환경에서의 레디스 접근 라이브러리 (spring data redis에 lettuce 내장되어 있음)
-  커넥션 핸들링 제공 (우리는 standalone 연결중임)
- 동기, 비동기, 리액티브 API 제공
- 클러스터를 지원(별도 설정 필요), but 우리는 standalone으로 사용중이라 필요 없음

### RedisTemplate
`   redistemplate`은 Redis 서버와 상호작용하기 위한 Java 언어용 템플릿입니다. Redis는 인메모리 데이터 저장소로서 키-값 쌍을 저장하고 검색하는 데 사용되며, `redistemplate`은 Java 애플리케이션에서 이러한 Redis 서버와 통신하는 데 도움을 주는 도구입니다.

Spring Data Redis 프로젝트에서 제공되는 `StringRedisTemplate`과 `RedisTemplate`은 `redistemplate`의 구현 예입니다. 이 템플릿들은 간단한 인터페이스를 제공하여 Java 애플리케이션이 Redis 데이터베이스와 상호작용할 수 있게 도와줍니다. 주로 문자열, 리스트, 해시 맵 등과 같은 다양한 데이터 유형을 다룰 수 있습니다.

예를 들어, 문자열 값을 저장하거나 가져오는 데 사용되는 간단한 메소드들이 있습니다. 이러한 템플릿은 Redis 명령을 더 쉽게 실행하고 결과를 처리할 수 있도록 해주어 개발자들이 Redis와의 통합을 더욱 편리하게 할 수 있게 도와줍니다.

> ### 나만 몰랐던 redis 사용법
> 
> 기본적으로 opsForX() 메서드는 공통적으로 get, set 메서드를 지원한다. 여기서 setIfAbsent() 와 같은 파생 메서드도 있지만, 일단 기본적으로 set을 기준으로 설명한다.
> 
> set() 메서드는 기본적으로 해당 key가 redis DB 안에 없으면 새로 생성한다.
> 만약 이미 존재하는 key에 대하여 set() 메서드를 사용했다면, 데이터를 수정한다.

### 자료구조
레디스는 다양한 상황에 최적화되게 적용할 수 있도록 많은 자료구조를 지원하고 있다.
1. String
2. List
3. Set
4. ZSet
5. Hash
*** 각 자료구조의 특성을 알고 사용할 수 있도록 함.

또한 각 자료구조의 operation들을 사용할 수 있도록 opsForX() 메서드들을 지원하고 있다.
ex) `opsForValue()`, `opsForList()`, `opsForSet()`, `opsForZSet()`, `opsForHash()`