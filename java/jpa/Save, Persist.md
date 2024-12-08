### persist

   - JPA의 EntityManager 가 제공하는 메서드
     - 새로운 엔티티를 영속성 컨텍스트에 추가하되, DB 에는 저장하지 
      않음
     - 트랜잭션이 커밋될 때 flush() 호출로 저장된다. (flush 는 엔티티 신규 저장, 변경 저장을 반영하며 트랜잭션 종료 시 자동으로 호출됨)
     - 반환 값이 없음
     - 이미 영속성 컨텍스트에 존재하는 객체에 대해 호출하면 예외가 발생한다(EntityExistsException).

```java
Member member = new Member();
member.setName("John Doe");

entityManager.persist(member);
```

### save

- Spring Data JPA의 JpaRepository 가 제공하는 메서드
  - 호출 시 영속성 컨텍스트뿐 아니라 DB 에 즉시 반영된다.
    - 새로운 엔티티면 persist()를 호출해 저장
    - 이미 존재하는 엔티티면 merge()를 호출해 상태를 병합(업데이트)한다.
  - 반환 값이 있으며, 저장된 엔티티를 반환한다.

```java
Member member = new Member();
member.setName("John Doe");

Member savedMember = memberRepository.save(member);
```