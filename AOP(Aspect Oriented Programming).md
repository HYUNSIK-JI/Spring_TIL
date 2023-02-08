# AOP(Aspect Oriented Programming)



* ex) AOP가 필요한 상황

  모든 메소드의 호출 시간을 측정하고 싶다면 ?
  -> 시간을 측정하는 로직은 공통 관심 사항 -> 모든 로직을 찾아가면서 변경해야함 -> 복잡 & 어렵다!

  AOP를 적용한다는 것

  -공통 관심 사항을 분리하고
  -시간을 측정하는 별도의 로직을 만들어 원는 적용 대상만을 선택 !
![스프링 AOP1](https://user-images.githubusercontent.com/59475851/217503665-fbd985bb-89f0-4041-9563-e928aa46a9f2.png)


AOP 적용 후

@Componenet, @Aspect 애노테이션을 통해 스프링 빈으로 등록하여 AOP사용
@Around는 어느 시점에 적용할지에 대해 패턴으로 정의하는 애노테이션



```java
package hello.hellospring.aop;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class TimeTraceAop {

    @Around("execution(* hello.hellospring..*(..))")
    public Object execut(ProceedingJoinPoint joinPoint) throws Throwable {
        long start = System.currentTimeMillis();
        System.out.println("start:" + joinPoint.toString());
        try {
            return joinPoint.proceed();
        } finally {
            long finish = System.currentTimeMillis();
            long timeMs = finish - start;
            System.out.println("END:" + joinPoint.toString() + " " + timeMs + "ms");


        }
    }
}

```



### 해결

* 회원가입, 회원 조회등 핵심 관심사항과 시간을 측정하는 공통 관심 사항을 분리한다
* 시간을 측정하는 로직을 별도의 공통 로직으로 만들었다
* 핵심 관심 사항을 깔끔하게 유지할 수 있다.
* 변경이 필요하면 이 로직만 변경하면 된다.
* 원하는 적용 대상을 선택할 수 있다.



### 스프링 AOP 동작 방식 설명

* AOP 적용 전 의존관계


  ![AOP 적용 전 의존관계](https://user-images.githubusercontent.com/59475851/217503841-037c92f9-807f-40b0-9189-99cdb90fb9be.png)



* helloController는 memberService의 기능들을 사용하며 의존관계가 수립된다. 

* 컨트롤러에서는 memberService의 실제 객체(Real Subject)에 바로 접근하여 로직을 수행시킨다.

* memberService 에서는 공통적인 기능들을 추가하려면 각각의 메서드마다 기능들을 추가/수정/삭제 해야한다.



* AOP 적용 후 의존 관계
  ![AOP 적용 후 의존관계](https://user-images.githubusercontent.com/59475851/217503928-9cea82d8-e10d-4beb-b6ab-5d1a6bdbbbdd.png)

* helloController와 memberService 간에 프록시 객체인 memberServiceProxy가 추가되었다.
* helloController 는 이제  실제객체(Real Subject)에 바로 접근하지않고 프록시객체를 거쳐 프록시 객체에서 실제 객체에 접근하여 로직을 수행한다.



* AOP 적용 전 전체 그림

  ![AOP 적용 전 전체 그림](https://user-images.githubusercontent.com/59475851/217503964-e8e232b7-54d5-4bb8-a0e5-0e3535c743bf.png)


* Client가 Request를 하였을 때 스프링 컨테이너 내부의 의존관계를 보여준다.
* helloController 가 memberService 에 메서드를 호출해 비즈니스 로직을 수행하고 memberService에서는 memberRepository 를 호출하여 레파지토리에서는 DB에 접근해 데이터를 조회/수정/삭제 한다.
* 모두 실제객체에 바로바로 접근한다.



* AOP 적용 후 전체 그림

 ![AOP 적용 후 의존관계](https://user-images.githubusercontent.com/59475851/217504001-b4c860a0-0988-4055-b45d-f6808cc7887b.png)


* 모든 객체들에게 프록시객체가 생성되었고 의존관계도 프록시 객체를 통하여 이뤄진다.
* 각각 객체의 기능들을 수행하려 메서드를 호출하면 요청을 프록시 객체가 전달받아서 전처리/후처리 등 추가적인 작업을 수행하면서 실제 객체(Real Subject)에 로직을 수행한다.
