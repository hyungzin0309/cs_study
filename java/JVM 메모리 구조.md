JVM(Java Virtual Machine)의 메모리 구조는 여러 영역으로 나뉘며, 각각의 영역이 특정한 역할을 담당한다. JVM 메모리는 크게 **Heap 영역, Method 영역, Stack 영역, PC 레지스터, Native Method Stack** 으로 구성된다.

---

## 1. JVM 메모리 구조

### 1.1 Heap 영역
- **객체(Instance)와 배열(Array)가 저장되는 공간**
- **GC(Garbage Collector)**가 관리하며, 더 이상 사용되지 않는 객체를 자동으로 정리함
- 크기가 크고, 동적 할당이 이루어지는 공간
- 다시 **Young Generation**과 **Old Generation**으로 나뉨

#### (1) Young Generation
- 새롭게 생성된 객체가 저장되는 영역
- **Eden, Survivor 0, Survivor 1** 로 나뉘어 관리됨
    - **Eden 영역**: 새롭게 생성된 객체가 저장됨
    - **Survivor 영역(0, 1)**: GC에서 살아남은 객체들이 이동하는 공간

#### (2) Old Generation
- Young 영역에서 오래 살아남은 객체들이 이동하는 공간
- 크기가 크고, GC가 비교적 적게 발생함 (하지만 한번 발생하면 시간이 오래 걸릴 수 있음)
- **Full GC**가 발생하는 주요 영역

---

### 1.2 Method 영역 (또는 Metaspace - Java 8부터 변경)
- **클래스의 메타데이터(클래스 정보, 메서드 정보, static 변수 등)가 저장되는 공간**
- **Runtime Constant Pool**(상수 풀)도 포함됨
- Java 7까지는 **PermGen(Permanent Generation, 영구 세대)** 라는 영역이 존재했으나, Java 8부터 **Metaspace**로 대체됨
    - **Metaspace**는 PermGen과 달리 Heap이 아니라 네이티브 메모리에 할당됨
    - 자동 확장이 가능하여 PermGen의 OutOfMemoryError 문제를 개선함

---

### 1.3 Stack 영역
- 각 **쓰레드(Thread)** 마다 생성되는 공간
- **메서드 호출 시 (스택 프레임이)생성되며, 메서드가 종료되면 제거되는 구조** (LIFO - Last In First Out)
- **프레임(Frame) 단위로 관리**되며, 프레임 내부에는 다음 요소가 존재함
    - **Local Variable Array** (지역 변수 저장)
    - **Operand Stack** (연산을 수행할 때 사용하는 스택)
    - **Frame Data** (현재 실행 중인 메서드의 정보)

---

### 1.4 PC(Program Counter) 레지스터
- **현재 실행 중인 명령어의 주소를 저장하는 공간**
- 각 쓰레드마다 독립적으로 존재
- **Native 메서드를 실행할 경우, 특정한 PC 값을 가지지 않을 수도 있음**

---

### 1.5 Native Method Stack
- **JNI(Java Native Interface)를 통해 호출되는 네이티브 코드(C, C++ 등) 관련 정보가 저장됨**
- C/C++에서 사용하는 스택과 유사하며, 네이티브 메서드 실행 시 생성됨
- C 코드 자체를 저장하는 공간이 아닌, C 를 실행하기 위한 스택 메모리 영역임.
- C 코드 자체는 OS가 관리하는 네이티브 메모리 영역(코드 세그먼트, Text Segment)에 로드됨

---

## 2. JVM 메모리 구조와 GC

JVM은 GC를 사용하여 Heap 영역을 관리하며, 주요 GC 알고리즘은 다음과 같다.

### 2.1 Minor GC (Young GC)
- **Young Generation에서 발생하는 GC**
- Eden에서 생성된 객체가 Survivor 영역으로 이동하며, 일정 시간이 지나면 Old 영역으로 승격

### 2.2 Major GC (Old GC)
- **Old Generation에서 발생하는 GC**
- 오래된 객체들을 정리하며, 속도가 느릴 수 있음

### 2.3 Full GC
- **Heap 전체(Method 영역 포함)를 대상으로 수행되는 GC**
- Minor GC와 Major GC를 모두 수행하므로 실행 시간이 길어 성능에 영향을 줄 수 있음

---

## 3. JVM 메모리 튜닝
- `-Xms` : 초기 Heap 크기 설정
- `-Xmx` : 최대 Heap 크기 설정
- `-XX:NewRatio` : Young과 Old 영역 비율 조정
- `-XX:SurvivorRatio` : Eden과 Survivor 비율 조정
- `-XX:MaxMetaspaceSize` : Metaspace 최대 크기 설정
