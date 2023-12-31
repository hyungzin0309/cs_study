# CPU 스케줄링
> CPU 스케줄링이란, 운영체제가 다수의 프로세스 또는 스레드 사이에서 CPU 사용 시간을 어떻게 할당하고 관리할지 결정하는 과정을 말한다.
> 이는 시스템의 자원을 효율적으로 관리하고, 프로세스의 실행을 공정하게 조정하기 위해 필요한 작업이다.

CPU 스케줄링은 크게 비선점형과 선점형 두 가지 방식으로 분류된다.

---
## 비선점형 스케줄링  
> 비선점형 스케줄링은 한 번 CPU를 할당받은 프로세스가 작업을 완료하거나 대기 상태로 전환될 때까지 CPU를 계속 사용할 수 있도록 하는 방식이다.
> <br>이러한 특성 때문에, 비선점형 스케줄링은 간단하고 예측 가능하지만, 특정 프로세스가 CPU를 독점할 위험이 있다. 주요 비선점형 스케줄링 알고리즘은 다음과 같다.

### 1. 선입 선출(FIFO) 또는 FCFS(First-Come, First-Served)

- 가장 간단한 비선점형 스케줄링 방식으로, 먼저 도착한 프로세스가 먼저 CPU를 할당받는다.
- 공정하고 예측 가능하지만, 짧은 작업이 긴 작업 뒤에 올 경우 긴 대기 시간이 발생할 수 있다 (호위 효과, convoy effect).

### 2. 최단 작업 우선(SJF, Shortest Job First)

- 실행 시간이 가장 짧은 프로세스에게 먼저 CPU를 할당한다.
- 평균 대기 시간을 최소화할 수 있지만 프로세스의 실행 시간을 미리 예측해야 하고, 긴 작업이 계속해서 대기할 수 있다(기아 현상).

### 3. 우선순위 스케줄링(Priority Scheduling)

- 프로세스에 우선순위를 부여하고, 높은 우선순위를 가진 프로세스에게 먼저 CPU를 할당한다.
- 중요한 작업을 신속하게 처리할 수 있지만, 낮은 우선순위의 프로세스가 계속 대기하는 기아 상태(starvation)가 발생할 수 있다.

### 4. SFJ + 우선순위 스케줄링

- 실행시간이 짧은 프로세스에게 먼저 CPU를 할당하는 방식인 SFJ의 치명적인 문제는, 실행시간이 긴 프로세스의 우선순위가 계속 밀려나 starvation 될 수 있다는 것이다.
<br>process 의 대기시간이 길어질수록 우선순위를 부여하는 방식(aging에 기반한 우선순위 부여)을 결합하면 starvation을 방지할 수 있다.
<br><br>

---

## 선점형 스케줄링

> 선점형 스케줄링은 운영체제가 현재 실행 중인 프로세스를 중단하고 다른 프로세스에게 CPU를 할당하는 스케줄링 방식이다.
> 이 방식은 시스템의 반응 시간을 줄이고 더 공정한 자원 분배를 가능하게 한다.  

현대의 운영체제는 시스템의 반응성과 멀티태스킹 능력을 향상시키기 위해 주로 선점형 스케줄링 방식을 사용한다.
<br>주요 선점형 스케줄링 알고리즘은 다음과 같다.

### 1. 라운드 로빈(Round Robin)

- 각 프로세스는 일정 시간(타임 퀀텀) 동안만 CPU를 사용한다. 시간이 끝나면 다음 프로세스로 넘어가므로 모든 프로세스가 동일한 기회를 가진다.
- 타임 퀀텀 설정은 시스템의 성능과 반응 시간에 중요한 역할을 한다.
<br> 타임 퀀텀의 길이가 너무 길면 단순 FCFS 와 같이 동작해버릴 수 있고, 너무 짧으면 컨텍스트 스위칭이 자주 발생하여 오버헤드가 증가하기 때문에 적절한 타임퀀텀을 설정하는 것이 중요하다.
### 2. 우선순위 기반 선점 스케줄링(Preemptive Priority Scheduling)

- 프로세스에 우선순위를 부여하고, 높은 우선순위를 가진 프로세스에게 먼저 CPU를 할당한다.
- 새 프로세스가 도착하거나 우선순위가 높은 프로세스가 준비 상태가 되면 현재 실행 중인 프로세스를 중단하고 CPU를 재할당한다.
-  긴급하거나 중요한 작업을 빠르게 처리할 수 있으나 낮은 우선순위의 프로세스가 기아 상태에 빠질 수 있다. 또한, 우선순위 결정이 복잡할 수 있다.
### 3. 최소 잔여 시간 우선(Shortest Remaining Time First, SRTF)

- 남은 실행 시간이 가장 짧은 프로세스에게 우선적으로 CPU를 할당한다.
- 새로운 프로세스가 도착할 때마다 남은 시간을 비교하여 스케줄링한다. (실행중인 프로세스를 멈추고 실행시간이 짧은 프로세스를 실행시킨다.)
- 짧은 작업을 빠르게 처리하여 시스템의 응답 시간을 최소화한다. 데이터 처리 작업이 많은 시스템에서 유용하다.
  그러나 긴 작업이 계속해서 대기 상태에 머무를 수 있으며, 실행 시간 예측이 어렵다.

### 4. 멀티 레벨 큐 스케줄링 (Multi-Level Queue)

- 프로세스를 여러 개의 큐로 분류하고, 각 큐는 고유한 우선순위를 가지며, 각 큐는 자체적인 스케줄링 알고리즘(예: 라운드 로빈, 우선순위 스케줄링 등)을 사용할 수 있다. 
<br>예를 들어, 시스템 프로세스, 대화형 사용자 프로세스, 배치 프로세스 등으로 구분하여 관리할 수 있다.

- 다양한 종류의 프로세스 요구 사항을 효과적으로 처리할 수 있다.
- 큐 사이의 프로세스 이동 관리가 복잡하고 낮은 우선순위의 큐에 있는 프로세스에 대해 기아 현상이 발생할 수 있다.