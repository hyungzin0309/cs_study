> 낙관적 락(Optimistic Lock)과 비관적 락(Pessimistic Lock)은 데이터의 **동시성 문제**를 해결하기 위해 사용되는 방식이다. 각각의 개념과 동작 방식, 장단점을 아래와 같이 정리할 수 있다.

---

### 1. 낙관적 락 (Optimistic Lock)
낙관적 락은 **충돌이 드물 것**이라고 가정하고 동작하는 방식이다. 데이터의 동시 수정 충돌을 감지하기 위해 **버전 관리**를 사용한다.

#### 특징
- 데이터 수정 시점에 **버전 번호**를 확인하여 동시성 문제를 해결한다.
- 충돌이 발생하지 않을 것으로 낙관적으로 가정하므로 **락을 잡지 않고 자유롭게 작업**한다.
- 데이터를 읽는 시점에는 락이 걸리지 않으며, 데이터를 수정하거나 저장할 때만 충돌 여부를 검사한다.

#### 동작 방식
1. 엔티티에 `@Version` 필드를 추가하여 버전 번호를 관리한다.
2. 데이터를 읽어온 뒤, 수정 및 저장할 때 버전 번호를 확인한다.
3. 버전 번호가 일치하지 않으면 충돌이 발생한 것으로 간주하여 업데이트를 실패시킨다.

#### 장점
- 락을 걸지 않으므로 **동시성 제어 비용**이 적다.
- 읽기 작업이 많은 환경에서 적합하다.
- 데이터베이스 락이 없어 **데드락(Deadlock)** 이 발생하지 않는다.

#### 단점
- **충돌 발생 시** 데이터를 다시 읽어와야 하므로 일부 재처리가 필요하다.
- 충돌이 자주 발생하는 환경에서는 비효율적이다.

#### 구현 예시
```java
@Entity
public class Product {
@Id
private Long id;

    @Version
    private int version;

    private String name;
    private int stock;
}
```

```java
Product product = em.find(Product.class, 1L); // 데이터 읽기

product.setStock(product.getStock() - 1); // 데이터 수정

em.merge(product); // 데이터 저장 시 버전 번호 체크
```

---

### 2. 비관적 락 (Pessimistic Lock)
비관적 락은 **충돌이 자주 발생할 것**이라고 가정하고 동작하는 방식이다. 데이터를 읽거나 수정할 때 **락을 걸어 다른 트랜잭션의 접근을 차단**한다.

#### 특징
- **DB 락**을 통해 동시성을 제어한다.
- 데이터를 읽는 시점 또는 수정하는 시점에 락을 건다.
- 다른 트랜잭션은 락이 해제될 때까지 대기해야 한다.

#### 락 모드
- **PESSIMISTIC_READ**: 데이터를 읽는 동안 다른 트랜잭션이 데이터를 수정하지 못하게 한다.
- **PESSIMISTIC_WRITE**: 데이터를 읽는 동안 다른 트랜잭션이 데이터를 읽거나 수정하지 못하게 한다.

#### 장점
- 충돌이 자주 발생하는 환경에서 데이터 무결성을 확실히 보장한다.
- **실시간 업데이트**가 빈번한 경우에 적합하다.

#### 단점
- 락으로 인해 **대기 시간이 길어질 수 있다**.
- 데드락(Deadlock) 발생 가능성이 있다.
- 락을 거는 과정에서 추가적인 DB 비용이 발생한다.

#### 구현 예시
```java
Product product = em.find(Product.class, 1L, LockModeType.PESSIMISTIC_WRITE); // 락 모드 설정

product.setStock(product.getStock() - 1); // 데이터 수정

em.merge(product); // 데이터 저장
```

#### mysql 명령어에서 lock 거는 예시
```mysql
START TRANSACTION;
SELECT * FROM orders WHERE id = 1 FOR UPDATE;
UPDATE orders SET status = 'processed' WHERE id = 1;
COMMIT;
```
---

#### 트랜잭션 격리성과의 차이

> 비관적 락은 하나의 트랜잭션이 자신의 임무를 수행할 때 스스로 락을 거는 것이다. 즉, 해당 락은 특정 트랜잭션에 의해서만 임시적으로 발생하는 것이며 락을 건 트랜잭션이 종료되면 락도 사라진다.<br>
 반면, 트랜잭션 격리성은 데이터베이스 전반에 대한 설정을 통해 이뤄지며 모든 트랜잭션에 대해 동일하게 적용된다.