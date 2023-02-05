# 스프링 데이터 JPA



스프링 부트와 JPA만 사용해도 개발 생산성이 정말 많이 증가하고, 

개발해야할 코드도 확연히 줄어듭니다.

여기에 스프링 데이터 JPA를 사용하면,

기존의 한계를 넘어 마치 마법처럼, 

리포지토리에 구현 클래스 없이

인터페이스 만으로 개발을 완료할 수 있습니다. 

그리고 반복 개발해온 기본 CRUD 기능도 스프링 데이터 JPA가 모두 제공합니다.

스프링 부트와 JPA라는 기반 위에, 스프링 데이터 JPA라는 환상적인 프레임워크를 더하면 개발이 정말
즐거워집니다. 

지금까지 조금이라도 단순하고 반복이라 생각했던 개발 코드들이 확연하게 줄어듭니다. 

따라서 개발자는 핵심 비즈니스 로직을 개발하는데, 집중할 수 있습니다.

실무에서 관계형 데이터베이스를 사용한다면 스프링 데이터 JPA는 이제 선택이 아니라 필수 입니다.

-김영한-



### 스프링 데이터 JPA 회원 리포지토리

```java
package hello.hellospring.repository;

import hello.hellospring.domain.Member;
import jakarta.persistence.EntityManager;
import org.springframework.transaction.annotation.Transactional;

import java.util.List;
import java.util.Optional;


public class JpaMemberRepository implements MemberRepository {

    private final EntityManager em;

    public JpaMemberRepository(EntityManager em) {
        this.em = em;
    }
    @Override
    public Member save(Member member) {
        em.persist(member);
        return member;
    }

    @Override
    public Optional<Member> findById(Long id) {
        Member member = em.find(Member.class, id);

        return Optional.ofNullable(member);
    }

    @Override
    public Optional<Member> findByName(String name) {
        // findByname 은 JPQ라는 특별한 쿼리문을 보내야한다.
        // pk 기반이 아닌 친구들은 jpq 쿼리문을 보내야한다.
        List<Member> result = em.createQuery("select m from Member m where m.name =: name", Member.class)
                .setParameter("name", name)
                .getResultList();
        return result.stream().findAny();
    }

    @Override
    public List<Member> findAll() {
        // JPQ라는 특별한 쿼리문을 보내야한다.
        // Member 엔티티를 조회하라는 의미이다.
        // select m -> 객체 자체를 select 한다.
        return em.createQuery("select m from Member m", Member.class)
                .getResultList();
    }
}

```



### 스프링 데이터 JPA 회원 리포지토리를 사용하도록 스프링 설정 변경



```java
package hello.hellospring.service;

import hello.hellospring.repository.*;
import jakarta.persistence.EntityManager;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.sql.DataSource;

@Configuration
public class SpringConfig {


    private EntityManager em;

    @Autowired
    public SpringConfig(EntityManager em) {
        this.em = em;
    }
    @Bean
    public MemberService memberService() {
        return new MemberService(memberRepository());
    }

    @Bean
    public MemberRepository memberRepository() {
//        return new MemoryMemberRepository();
//        return new JdbcMemberRepository(dataSource);
//        return new JdbcTemplateMemberRepository(dataSource);
        return new JpaMemberRepository(em);
    }

}

```



스프링 데이터 JPA가 SpringDataJpaMemberRepository 를 스프링 빈으로 자동 등록해준다.	

![스프링 데이터 jpa](C:\Users\user\OneDrive\바탕 화면\스프링이미지\스프링 데이터 jpa.png)



* 스프링 데이터 JPA 제공 기능
* 인터페이스를 통한 기본적인 CRUD
* findByName() , findByEmail() 처럼 메서드 이름 만으로 조회 기능 제공
* 페이징 기능 자동 제공