## 1. Call by Value vs Call by Reference

프로그래밍에서 함수(메서드) 호출 방식은 **Call by Value(값 호출)** 와 **Call by Reference(참조 호출)** 두 가지 방식이 있다.

---

### 1.1 Call by Value (값에 의한 호출)
- **함수에 인자로 전달되는 값이 복사되어 전달되는 방식**
- 즉, **원본 변수의 값이 변경되지 않음**
- 복사된 값만 변경될 뿐, 원래의 값에는 영향을 주지 않음

**예제 (Call by Value) - C언어**
```c
#include <stdio.h>

void changeValue(int x) {
x = 100;  // x의 값만 변경됨, 원본 변수는 영향 없음
}

int main() {
int a = 10;
changeValue(a);
printf("%d\n", a);  // 출력: 10 (a의 값은 그대로)
return 0;
}
`````

- `changeValue(a)`에서 `a`의 값이 복사되어 함수에 전달되므로, 함수 내부에서 변경해도 원본 값(`a`)은 영향을 받지 않음.

---

### 1.2 Call by Reference (참조에 의한 호출)
- **변수의 주소(참조)가 전달되는 방식**
- 즉, 함수 내부에서 값을 변경하면 원본 데이터도 변경됨
- 주로 C/C++에서 포인터를 사용하여 구현

**예제 (Call by Reference) - C언어**
```c
#include <stdio.h>

void changeValue(int *x) {
*x = 100;  // 포인터를 통해 원본 변수의 값을 변경
}

int main() {
int a = 10;
changeValue(&a);  // a의 주소를 전달
printf("%d\n", a);  // 출력: 100 (a의 값이 변경됨)
return 0;
}
````

- `&a`를 전달하여 `a`의 주소를 함수에 넘김.
- 함수 내부에서 `*x = 100;` 을 통해 원본 값이 직접 변경됨.

---

## 2. Java는 Call by Value인가 Call by Reference인가?
### **Java는 오직 "Call by Value" 만 존재**
- **Java에서 모든 메서드 호출은 값에 의한 호출(Call by Value) 방식으로 동작함**
- **기본 데이터 타입(Primitive Type)** 과 **객체(Object, Reference Type)** 에 따라 동작 방식이 다름

### 2.1 기본 타입(Primitive Type)은 Call by Value
```java
public class CallByValueExample {
public static void changeValue(int x) {
x = 100;  // x의 값만 변경됨, 원본 변수는 영향 없음
}

    public static void main(String[] args) {
        int a = 10;
        changeValue(a);
        System.out.println(a); // 출력: 10 (a의 값은 변경되지 않음)
    }
}
````

- `a`의 값이 메서드로 전달될 때 복사됨.
- 함수 내부에서 `x = 100;` 으로 변경해도 원본 값에는 영향 없음.

### 2.2 참조 타입(Reference Type)도 Call by Value
- **참조 타입(객체)의 경우, 객체의 "참조 주소값"이 복사되어 전달됨**
- 즉, **참조 대상은 같지만, 참조 주소값을 가진 변수는 서로 다름**

**예제 (참조 타입)**
```java
class Person {
    String name;
}
    
public class CallByValueReferenceExample {
    public static void changeName(Person p) {
        p.name = "Alice";  // 객체의 속성 변경
    }

    public static void main(String[] args) {
        Person person = new Person();
        person.name = "Bob";

        changeName(person);
        System.out.println(person.name); // 출력: Alice (객체의 속성이 변경됨)
    }
}
````

- `changeName(person);` 호출 시, **객체의 참조 주소가 복사되어 전달됨**
- 따라서, `p.name = "Alice";` 를 통해 **객체의 속성은 변경될 수 있음**
- 하지만 **새로운 객체를 할당하면 원본 참조에는 영향이 없음**

**참조 자체 변경 시**
```java
public class CallByValueReferenceExample {
    public static void changeReference(Person p) {
        p = new Person();  // 새로운 객체를 할당
        p.name = "Alice";
    }

    public static void main(String[] args) {
        Person person = new Person();
        person.name = "Bob";

        changeReference(person);
        System.out.println(person.name); // 출력: Bob (참조 자체는 변경되지 않음)
    }
}
````

- changeReference 에서의 변수 p 와 main 함수의 변수 person 은 같은 객체를 참조하고 있으나, 각 스택 프레임 내에서는 다른 변수로 존재함.
- 즉, 참조값은 같아 원본에 변경을 가할 수는 있으나 각각의 스택에서 다른 변수로 존재하기 때문에 'Call By Value' 방식인 것임. 

---