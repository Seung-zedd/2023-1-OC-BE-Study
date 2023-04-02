# <section 3> 회원 관리 예제 - 백엔드 개발

section: Spring(김영한)
생성일: 2023년 4월 3일 오전 12:31

# 📚Topic covered

1. 컨트롤러, 서비스, 리포지토리의 역할
2. TDD란? 왜 하는가?

# 📖컨트롤러, 서비스, 리포지토리의 역할

## 📑비즈니스 요구사항 정리

- 데이터: 회원ID, 이름
- 기능: 회원 등록, 조회
- 아직 데이터 저장소가 선정되지 않음(**가상의 시나리오,** 그래서 interface로 구현)
    - 이 **interface**에 RDB, NoSQL 등 다양한 DB 저장소로 교체할 수 있기 때문에 **의존적이지 않음**
- 컨트롤러: 웹 MVC(Model, View, Controller의 dynamic engine)의 컨트롤러 역할, DB에 객체를 넣어줌
- 서비스: 핵심 비즈니스 **로직** 구현
- 리포지토리: 데이터베이스에 접근, 도메인 객체를 **DB에 저장하고 관리**
- 도메인: 비즈니스 도메인 객체
    - 예) 회원, 주문, 쿠폰 등등 주로 데이터베이스에 저장하고 관리됨

## 📋회원 도메인과 리포지토리 만들기

### 💻회원 객체(Member 클래스)

```java
package hello.hellospring.domain;

public class Member {
    private Long id; // Long은 wrapper class로, long 원시 타입을 객체로 감싼 것(reference type이라 null 값을 가질 수 있음)
    private String name;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

### 💻MemberRepository 인터페이스

```java
package hello.hellospring.repository;

import hello.hellospring.domain.Member;

import java.util.List;
import java.util.Optional;

public interface MemberRepository {
    Member save(Member member);
    Optional<Member> findById(Long id);
    Optional<Member> findByName(String name);
    List<Member> findAll();
}
```

### 💻회원 리포지토리 메모리 구현체(MemoryMemberRepository 클래스)

```java
package hello.hellospring.repository;

import hello.hellospring.domain.Member;

import java.util.*;

public class MemoryMemberRepository implements MemberRepository{

    private static Map<Long, Member> store = new HashMap<>();
    private static long sequence = 0L; // sequence: 0,1,2 숫자로 key값 생성
    @Override
    public Member save(Member member) {
        member.setId(++sequence);
        store.put(member.getId(), member);
        return member;
    }

    @Override
    public Optional<Member> findById(Long id) {
        return Optional.ofNullable(store.get(id)); // null이 있으면, 감싸서 반환
    }

    @Override
    public Optional<Member> findByName(String name) {
        return store.values().stream()
                .filter(member -> member.getName().equals(name))
                .findAny();
    }

    @Override
    public List<Member> findAll() {
        return new ArrayList<>(store.values());
    }
}
```

### 💻회원 리포지토리 메모리 구현체 테스트(MemoryMemberRepositoryTest 클래스)

🍯`@Test` 를 작성하고 `Alt + Enter` 를 누르면 알아서 `junit.jupiter.api.Test` 가 import된다

```java
package hello.hellospring.repository;

import hello.hellospring.domain.Member;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.Assertions;
import org.junit.jupiter.api.Test;

import java.util.List;

import static org.assertj.core.api.Assertions.*;

public class MemoryMemberRepositoryTest {
    MemoryMemberRepository repository = new MemoryMemberRepository();

    @AfterEach // Test가 끝날때마다 repository를 지워줌(순서에 의존적 x)
    public void afterEach(){
        repository.clearSotre();
    }

    @Test
    public void save(){
        Member member =new Member();
        member.setName("spring");
         repository.save(member);

        Member result = repository.findById(member.getId()).get();
        assertThat(member).isEqualTo(result);
    }

    @Test
    public void findByName(){
        Member member1 = new Member();
        member1.setName("spring1");
        repository.save(member1);

        // 꿀팁: Shift + F6을 누르면 변수명을 refactoring할 수 있음
        Member member2 = new Member();
        member2.setName("spring1");
        repository.save(member2);

        Member result = repository.findByName("spring1").get();

        assertThat(result).isEqualTo(member1); // "spring1"을 get한 다음, result에 넣고, 그것이 member1와 일치하면 테스트케이스 통과
    }

    @Test
    public void findAll(){
        Member member1 = new Member();
        member1.setName("spring1");
        repository.save(member1);

        Member member2 = new Member();
        member2.setName("spring1");
        repository.save(member2);

        List<Member> result = repository.findAll();

        assertThat(result.size()).isEqualTo(2);

    }

}
```

# 🤔TDD란? 왜 하는가?

- TDD는 Test-Driven Development(테스트 주도 개발)의 축약어이고, **선 테스트코드 작성, 후 실제 코드 개발을 하는 개발 방법론**을 일컫는다. 이렇게 하는 이유는 아무리 고객에 대한 요구 사항에 맞는 모듈 또는 객체를 개발했다고 하더라도, 테스트케이스를 통과하지 못하면 무용지물이기 때문이다.
    - 심지어는 객체를 100개 이상을 만들어서 선 개발을 지향했다고 하더라도, **테스트케이스**를 통과하지 못하면 *사상누각*이 되버리는 것이다

👉따라서, 개발자는 **요구 사항에 맞는 객체의 동작과 결과를 충분히 이해(구현 -> 테스트 -> 리펙토링)**하고 구현하는 것이 바람직하다.

# 📋회원 서비스 개발

## 💻회원 서비스 개발(MemberService 클래스)

```java
package hello.hellospring.service;

import hello.hellospring.domain.Member;
import hello.hellospring.repository.MemberRepository;
import hello.hellospring.repository.MemoryMemberRepository;

import java.util.List;
import java.util.Optional;

public class MemberService {
    private final MemberRepository memberRepository = new MemoryMemberRepository();

    /* 회원가입*/
    public Long join(Member member){
        // 같은 이름이 있는 중복 회원 x
        
        

        validateDuplicateMember(member); // 중복 회원 검증
        memberRepository.save(member);
        return member.getId();
    }

    private void validateDuplicateMember(Member member) {
        memberRepository.findByName(member.getName())
                .ifPresent(m -> { // getName의 이름이 중복되면
            throw new IllegalStateException("이미 존재하는 회원입니다.");
        });
    }

    /* 전체 회원 조회*/
    public List<Member> findMembers(){
        return memberRepository.findAll();
    }

    public Optional<Member> findOne(Long memberId){
        return memberRepository.findById(memberId);
    }
}
```

### 🍯꿀팁

- Ctrl + Alt + V: extract Vairable
- Ctrl + Alt + M: extract Method