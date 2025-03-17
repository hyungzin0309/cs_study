## Java의 Garbage Collection(GC)

> Java의 Garbage Collection(GC)은 **사용되지 않는 객체를 자동으로 제거하여 메모리를 효율적으로 관리하는 기능**이다. Java는 개발자가 명시적으로 메모리를 해제할 필요 없이 **GC가 불필요한 객체를 자동으로 삭제**한다.

---

## 1. GC의 동작 방식

Java의 메모리는 **Heap 영역**과 **Stack 영역**으로 나뉜다.  
Heap 영역에서 생성된 객체들은 **GC의 대상**이 되며, **더 이상 참조되지 않는 객체**는 GC에 의해 제거된다.

GC는 **Mark & Sweep 알고리즘**을 기반으로 동작하며, 다음과 같은 단계를 거친다.

### (1) Mark 단계
- GC가 실행되면 **Reachable Object(도달 가능한 객체)** 를 찾는다.
- 참조되지 않는 객체는 **Garbage(쓰레기 객체)** 로 인식된다.

### (2) Sweep 단계
- Garbage 객체를 메모리에서 제거한다.

### (3) Compact 단계 (필요할 경우)
- 메모리 단편화를 줄이기 위해 살아남은 객체들을 한쪽으로 모아 Heap 공간을 정리한다.

---

## 2. JVM Heap 영역과 GC 동작

### (1) Heap 영역의 구조
JVM의 Heap은 **Young Generation(Young 영역)** 과 **Old Generation(Old 영역)** 으로 나뉜다.

#### ▶ Young Generation (새롭게 생성된 객체가 저장)
- **Eden 영역** : 새로운 객체가 생성되는 공간
- **Survivor 0, Survivor 1** : Eden에서 살아남은 객체가 이동하는 공간
- **객체가 일정 횟수 이상 살아남으면 Old 영역으로 이동** (Promotion)

#### ▶ Old Generation (오래된 객체가 저장)
- Young 영역에서 오래 살아남은 객체가 이동하는 영역
- 이 영역에서 **Major GC (Old GC)** 가 발생함

#### ▶ Metaspace (Java 8부터 도입, 클래스 정보 저장)
- **클래스 메타데이터, static 변수, 상수 풀** 등이 저장되는 공간
- Java 7까지는 **PermGen (Permanent Generation)** 영역이 있었지만, Java 8부터 **Metaspace** 로 변경됨

---

## 3. GC의 종류

### (1) Minor GC (Young GC)
- **Young Generation에서 발생하는 GC**
- **Eden 영역이 가득 차면 실행**됨
- 사용되지 않는 객체를 제거하고 살아남은 객체는 **Survivor 영역으로 이동**
- 상대적으로 빠르게 실행됨

### (2) Major GC (Old GC)
- **Old Generation에서 발생하는 GC**
- 오래된 객체를 제거하는 과정
- **속도가 느리고 애플리케이션의 응답 속도에 영향을 줄 수 있음**
- **Full GC를 동반할 가능성이 높음**

### (3) Full GC
- **Heap 전체(Young + Old + Metaspace)를 대상으로 수행되는 GC**
- 실행 시간이 길고, 애플리케이션 성능에 큰 영향을 미칠 수 있음
- **최대한 발생하지 않도록 튜닝 필요**

---

## 4. Java의 GC 알고리즘 종류

### (1) Serial GC
- **단일 쓰레드 환경에서 실행되는 GC**
- 한 번에 한 개의 쓰레드가 GC를 수행 → **애플리케이션을 멈추고 GC 실행 (Stop-the-world)**
- 적은 메모리를 사용하는 환경에서 적합
- 실행 옵션: `-XX:+UseSerialGC`

### (2) Parallel GC (기본 GC)
- **여러 개의 쓰레드가 동시에 GC를 수행** (멀티코어 환경에 적합)
- Young GC를 병렬로 실행하여 성능 향상
- Old GC는 단일 쓰레드로 실행됨
- 실행 옵션: `-XX:+UseParallelGC`

### (3) CMS (Concurrent Mark-Sweep) GC (Java 9부터 Deprecated)
- **애플리케이션이 실행되는 동안(GC가 백그라운드에서 동작) 메모리를 정리**
- `Stop-the-world` 시간을 줄이기 위해 **Mark 단계와 Sweep 단계를 병렬 실행**
- **CPU 자원을 많이 사용하지만, 응답 속도가 중요한 애플리케이션에 적합**
- 실행 옵션: `-XX:+UseConcMarkSweepGC`

### (4) G1 (Garbage First) GC (Java 9 기본 GC)
- **Heap을 작은 Region 단위로 나누어 관리하는 방식**
- **GC를 병렬 실행하고, Stop-the-world 시간을 최소화**
- Full GC 발생 가능성을 줄이는 데 초점
- 실행 옵션: `-XX:+UseG1GC`

### (5) ZGC (Java 11+)
- **초저지연 GC(Low Latency GC)**
- **GC로 인한 Stop-the-world 시간을 10ms 이하로 유지**
- 메모리 크기가 10GB 이상인 대용량 애플리케이션에 적합
- 실행 옵션: `-XX:+UseZGC`

### (6) Shenandoah GC (Java 12+)
- **ZGC와 비슷하지만, GC가 병렬로 실행됨**
- Stop-the-world 시간이 짧아짐
- 실행 옵션: `-XX:+UseShenandoahGC`

---

## 5. GC 튜닝 및 설정

### (1) Heap 크기 조정
- `-Xms<size>` : 초기 Heap 크기 설정
- `-Xmx<size>` : 최대 Heap 크기 설정

### (2) GC 알고리즘 선택
- `-XX:+UseSerialGC` : Serial GC 사용
- `-XX:+UseParallelGC` : Parallel GC 사용 (기본)
- `-XX:+UseG1GC` : G1 GC 사용
- `-XX:+UseZGC` : ZGC 사용

### (3) GC 로그 출력
- `-XX:+PrintGCDetails -Xlog:gc` : GC 실행 로그 출력

---

## 6. GC 최적화 전략
1. **객체의 수명에 맞게 메모리를 할당**
    - 단기 객체는 Eden에서 빠르게 제거되도록 유도
    - 장기 객체는 Old 영역으로 이동을 최소화

2. **Full GC를 최소화**
    - 너무 많은 객체가 Old 영역으로 이동하지 않도록 관리
    - GC 로그를 분석하여 불필요한 객체 생성을 줄임

3. **적절한 GC 알고리즘 선택**
    - 서버 환경: `G1 GC` or `ZGC` 추천
    - 저사양 환경: `Serial GC` or `Parallel GC`
    - 응답 시간이 중요한 환경: `CMS GC` or `Shenandoah GC`

4. **Heap 크기 최적화**
    - 메모리 부족으로 Full GC가 발생하지 않도록 적절한 Heap 크기 설정

---

## 7. 결론
Java GC는 자동으로 메모리를 관리하는 중요한 기능이다.  
GC의 종류와 동작 방식을 이해하고, 애플리케이션의 특성에 맞게 적절한 GC 알고리즘을 선택하는 것이 중요하다.  
또한 GC 튜닝을 통해 성능을 최적화할 수 있으며, **GC 로그 분석을 통해 문제를 파악하고 해결하는 것이 효과적인 관리 방법**이다.  