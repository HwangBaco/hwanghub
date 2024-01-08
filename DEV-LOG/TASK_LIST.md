## 백엔드

### 활동 증명
- [ ] (지식 공유) 멋사 기술 세미나에서 진행한 PPT를 PDF로 출력하여 블로그에 올려둘 것
-> 지식 공유를 해본 경험 증명 필수, 어떻게 공유했는지 면접에서 서술해야함
- [ ] (기술 스택) 토스 JD 기준으로 요구 스택 맞추는 풀스택 프로젝트 제작하기
- [ ] (활동 증명) 송곳매, 멋대 회고 반드시 작성 + 풀스택 개발과정 반드시 기록
- [ ] (알고리즘) 매일 extra 1솔 하기 + 기록하기
- [ ] (CS-STUDY) 전공지식 스터디 기간동안 성실하게 정리하기

### 자바
- (도서) 이것이 자바다

### 스프링
- (강의/인프런) 김영한 스프링/JPA 풀코스
- 백기선

### 기타 백엔드 개발 테마

1. dto들을 record로 리팩하기
https://scshim.tistory.com/372
https://blog.hexabrain.net/399
https://velog.io/@oyoungsun/Java-RECORD-DTO%EB%A5%BC-record-type%EC%9C%BC%EB%A1%9C-%EC%84%A4%EC%A0%95%ED%95%98%EB%8A%94-%EC%9D%B4%EC%9C%A0

https://jerry92k.tistory.com/16
---
record 는 기본이 private (class는 기본이 public)

- record dto () {} -> private
- public record dto () {} -> public
---

2. servicedto와 controllerdto로 리팩하기
https://developer-ping9.tistory.com/261
https://techblog.woowahan.com/2711/

https://kafcamus.tistory.com/12?category=912020
3. 스태틱팩토리메서드랑 빌더 패턴 비교하고 적용

4. 레디스를 사용해야 하는 이유
https://velog.io/@mu1616/%EB%A0%88%EB%94%94%EC%8A%A4%EC%99%80-%EC%8B%B1%EA%B8%80%EC%8A%A4%EB%A0%88%EB%93%9C

5. type parameter T vs. wildcard ?
https://www.baeldung.com/java-generics-type-parameter-vs-wildcard
https://stir.tistory.com/343

6. requestDto가 역직렬화되는 과정
https://jaehoney.tistory.com/287
https://kdg-is.tistory.com/entry/JAVA-%EB%A6%AC%ED%94%8C%EB%A0%89%EC%85%98-Reflection%EC%9D%B4%EB%9E%80
https://m-ioi-m.tistory.com/48?category=993220
https://codingwell.tistory.com/182

7. [기록] 트랜잭션 어노테이션을 안걸면 트랜잭션이 안걸리는건가?
https://www.inflearn.com/questions/227574/transactional-%EC%96%B4%EB%85%B8%ED%85%8C%EC%9D%B4%EC%85%98-%EC%A7%88%EB%AC%B8%EB%93%9C%EB%A6%BD%EB%8B%88%EB%8B%A4

8. 스웨거 Pageable 객체 파라미터로 받기
https://goodteacher.tistory.com/508

9. 페이지네이션 정리
https://jojoldu.tistory.com/476
https://jojoldu.tistory.com/528
https://jojoldu.tistory.com/529

10. s3로 https 배포하기
https://inpa.tistory.com/entry/AWS-%F0%9F%93%9A-S3-%EB%B2%84%ED%82%B7-%EC%83%9D%EC%84%B1-%EC%82%AC%EC%9A%A9%EB%B2%95-%EC%8B%A4%EC%A0%84-%EA%B5%AC%EC%B6%95

https://inpa.tistory.com/entry/AWS-%F0%9F%93%9A-S3-%EC%A0%95%EC%A0%81-%EC%9B%B9-%EC%82%AC%EC%9D%B4%ED%8A%B8-%ED%98%B8%EC%8A%A4%ED%8C%85-%EB%8F%84%EB%A9%94%EC%9D%B8-%EC%84%A4%EC%A0%95Route-53

https://sleepybird.tistory.com/144

11. 자바 공부내용 정리
- JVM architecture + 실행과정 정리
- 인터페이스 vs 추상클래스
- 제네릭클래스 정리
- 자바 리플렉션 정리
- 자바 직렬화 정리
https://techblog.woowahan.com/2550/
https://techblog.woowahan.com/2551/
https://www.happykoo.net/@happykoo/posts/257
- 문자열이 불변객체인 이유
https://youtu.be/Bj9Mx_Lx3q4?si=EM6gP2tB_MtBhltu

12. 스프링 ~ enum 파라미터 바인딩
https://velog.io/@haerong22/Enum-%ED%8C%8C%EB%9D%BC%EB%AF%B8%ED%84%B0-%EB%B0%94%EC%9D%B8%EB%94%A9
--
https://javanitto.tistory.com/43