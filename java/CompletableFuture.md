## CompletableFuture
> CompletableFuture는 자바 8에서 도입된 비동기 프로그래밍을 위한 클래스로, Future 인터페이스의 확장이다. 
> <br>이 클래스는 비동기 작업의 결과를 표현하고, 작업이 완료된 후에 수행할 행동을 정의할 수 있는 **콜백** 메커니즘을 제공한다.

### 필요성
- 비동기 작업의 조합
  - 여러 비동기 작업을 순차적이거나 병렬적으로 조합하여 실행할 수 있다.
- 비동기 작업의 연쇄
  - 하나의 작업이 완료된 후 추가적인 작업을 쉽게 연결할 수 있다.
- 에러 처리
  - 비동기 실행 중 발생하는 예외를 처리할 수 있다.
- 비동기 API
  - 외부 서비스 호출이나 긴 작업 처리 등 비동기적으로 수행될 작업을 위한 강력한 API를 제공한다.

### 비동기 작업 후의 처리 연결 1

```java
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutionException;

public class CompletableFutureExample {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        // 비동기 작업 생성 및 실행
        CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
            // 시간이 걸리는 작업 시뮬레이션
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                throw new IllegalStateException(e);
            }
            return "result";
        });

        // 작업 완료 후 실행할 작업 정의
        CompletableFuture<String> greetingFuture = future.thenApply(result -> "hello, " + result);

        // 최종 결과를 가져옴
        System.out.println(greetingFuture.get()); // "안녕, 결과"
    }
}

```

### 비동기 작업 후의 처리 연결 2

```java
public static void main(String[] args) {
    CompletableFuture<Void> future = CompletableFuture
            .supplyAsync(() -> "Hello")
            .thenApplyAsync(name -> name + " World")
            .thenAcceptAsync(result -> System.out.println(result));
    future.get();
}
```

### 여러 비동기 작업의 조합

```java
public static void main(String[] args) {
    CompletableFuture<String> future1 = CompletableFuture.supplyAsync(() -> {
        // 첫 번째 비동기 작업
        return "Hello";
    });
    CompletableFuture<String> future2 = CompletableFuture.supplyAsync(() -> {
        // 두 번째 비동기 작업
        return "World";
    });
    CompletableFuture<String> result = future1.thenCombine(future2, (s1, s2) -> s1 + " " + s2);
    System.out.println(result.get()); // "Hello World"
}
```

### 예외 처리

```java

public static void main(String[] args) {
    CompletableFuture<String> future = CompletableFuture
            .supplyAsync(() -> {
                if (true) throw new RuntimeException("오류 발생!");
                return "Hi";
            })
            .exceptionally(ex -> "Error: " + ex.getMessage());
    System.out.println(future.get());

}
```