## Executor
> 자바의 java.util.concurrent 패키지에 있는 Executor 인터페이스는 스레드의 생성과 관리를 단순화하는 데 도움을 주는 메커니즘이다. Executor를 사용하면 스레드를 더 효율적으로 사용하고 관리할 수 있다.

### Executor의 기능
- 스레드 풀 관리
  - Executor는 스레드 풀을 사용하여 스레드를 관리한다. 스레드 풀은 작업 처리를 위해 여러 스레드를 미리 생성하고 재사용한다.
- 작업 실행
  - Executor를 통해 별도의 스레드를 생성하지 않고도 비동기적으로 작업을 실행할 수 있다. execute(Runnable) 메소드를 사용하여 스레드 풀의 스레드에 작업을 할당한다.
  
### Executor의 활용
- ExecutorService
  - Executor 인터페이스를 확장한 ExecutorService는 스레드 풀의 라이프사이클 관리, 결과값을 반환하는 작업 실행 등 추가적인 기능을 제공한다.
- 스레드 풀 생성
  - Executors 유틸리티 클래스를 사용하여 다양한 종류의 스레드 풀을 쉽게 생성할 수 있다. 예를 들어, Executors.newFixedThreadPool(int)는 고정된 수의 스레드를 갖는 스레드 풀을 생성한다.

```java
public static void main(String[] args) {
    ExecutorService executor = Executors.newFixedThreadPool(4); // 4개의 스레드를 갖는 스레드 풀 생성
    
    // execute
    executor.execute(() -> {
        // 실행할 작업
    });

    // submit
    Callable<String> task = () -> {
        Thread.sleep(1000); // 1초 대기
        return "작업 결과";
    };

    Future<String> future = executorService.submit(task);

    String result = future.get(); // 작업의 결과를 기다린 후 반환받음
    System.out.println(result);

    executorService.shutdown();
}
```

### Runnable
- 결과를 반환하지 않는 run 메소드만 제공하는 함수형 인터페이스이다.
### Callable
- 결과를 반환하는 함수형 인터페이스이다.
- submit 을 호출하면 Callable 의 동작을 비동기적으로 수행함과 동시에 Callable 의 실행결과를 추적하는 Future 를 리턴한다.
<br>Future 의 get 을 호출하여 결과를 반환받을 수 있고, 만약 작업 완료 이전 get 이 호출되면 작업이 완료된 후 결과가 return 될 때까지 block 에 걸린다(결과가 나올 때까지 기다림).
