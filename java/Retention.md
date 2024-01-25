## Retention

> 어노테이션이 코드의 어느 단계까지 유지되는지를 정의하는 것을 말한다.
> <br>자바에서 어노테이션 유지는 주로 세 가지 범주로 나눌 수 있다.

### 소스(Source) 유지
- 이 유지 정책을 가진 어노테이션은 소스 코드 파일에서만 유지된다. 이는 주로 코드를 읽는 사람에게 유용한 정보를 제공하는 데 사용된다.
- ex) Override, Getter

### 클래스(Class) 유지
- 클래스 파일에 어노테이션 정보가 유지되지만, 런타임에는 JVM(Java Virtual Machine)에 의해 어노테이션 정보가 무시된다. 이는 컴파일러에 의해 처리되거나 기타 도구에 의해 사용될 수 있다.

### 런타임(Runtime) 유지
- 이 유지 정책을 가진 어노테이션은 런타임에 JVM에 의해 유지되고, 리플렉션을 통해 어노테이션 정보에 접근할 수 있다. 이는 런타임에 어노테이션 정보를 처리하는 라이브러리나 프레임워크에서 주로 사용된다.
- Transactional, Service, ... 