# JNI(Java Native Interface) 개념과 구현

## 1. JNI란?
**JNI(Java Native Interface)** 는 **자바와 네이티브 코드(C/C++) 간의 상호작용을 가능하게 하는 인터페이스**다.  
즉, **자바 코드에서 C/C++로 작성된 네이티브 라이브러리를 호출**하거나, 반대로 **C/C++에서 Java 메서드를 호출**할 수 있다.

✅ **네이티브 코드 활용 가능** (기존 C/C++ 라이브러리 재사용)  
✅ **성능 향상 가능** (자바보다 빠른 C/C++ 연산 활용)  
✅ **플랫폼 기능 접근** (OS API, 하드웨어 제어 가능)  
❌ **플랫폼 종속성** (OS마다 다른 바이너리 필요)  
❌ **메모리 관리 필요** (C/C++ 메모리는 GC가 관리하지 않음)

---

## 2. JNI 동작 과정 및 메모리 구조

### 2.1 JNI 호출 흐름
1. **Java에서 네이티브 메서드 선언** (`native` 키워드 사용)
2. **JNI 헤더 파일 생성** (`javah` 또는 `javac -h` 사용)
3. **C/C++에서 구현 작성**
4. **공유 라이브러리(.so, .dll) 컴파일**
5. **Java에서 네이티브 라이브러리 로드 (`System.loadLibrary()`)**
6. **JNI를 통해 Java와 C/C++ 간 데이터 교환**

### 2.2 JNI 메모리 구조
JNI는 **Java 힙과 네이티브 메모리 영역**을 다룬다.

| 메모리 영역 | 설명 |
|------------|------|
| **Java Heap** | Java 객체가 저장되는 영역 (Garbage Collector 관리) |
| **Java Stack** | 자바 메서드 호출 시 사용되는 스택 |
| **Native Heap** | C/C++ 네이티브 코드에서 할당하는 메모리 |
| **Native Stack** | 네이티브 메서드가 실행되는 스택 (JNI 호출 시 사용) |

**주의점**:
- 네이티브 코드에서 할당한 메모리는 **GC가 관리하지 않음**, 직접 해제해야 함 (`free()` 사용)
- 메모리 누수를 방지하려면 `DeleteLocalRef()` 등으로 해제해야 함

---

## 3. JNI 구현 예시

### 3.1 Java에서 네이티브 메서드 선언
```java
public class JNIDemo {
// 네이티브 메서드 선언 (C/C++에서 구현 예정)
public native int addNumbers(int a, int b);

    // 네이티브 라이브러리 로드
    static {
        System.loadLibrary("nativeLib"); // nativeLib.dll (Windows) 또는 libnativeLib.so (Linux)
    }

    public static void main(String[] args) {
        JNIDemo demo = new JNIDemo();
        int result = demo.addNumbers(5, 10);
        System.out.println("Result from C: " + result);
    }
}
```

---

### 3.2 JNI 헤더 파일 생성 (`javac -h . JNIDemo.java`)
위 코드를 컴파일하면, `JNIDemo.h` 파일이 생성됨.

```c
/* DO NOT EDIT THIS FILE - it is machine generated */
#include <jni.h>
#ifndef _Included_JNIDemo
#define _Included_JNIDemo
#ifdef __cplusplus
extern "C" {
#endif
JNIEXPORT jint JNICALL Java_JNIDemo_addNumbers(JNIEnv *, jobject, jint, jint);
#ifdef __cplusplus
}
#endif
#endif
```

---

### 3.3 C 코드 구현 (`JNIDemo.c`)
```c
#include <jni.h>
#include <stdio.h>
#include "JNIDemo.h"

// 네이티브 메서드 구현
JNIEXPORT jint JNICALL Java_JNIDemo_addNumbers(JNIEnv *env, jobject obj, jint a, jint b) {
return a + b;
}
```

---

### 3.4 공유 라이브러리 컴파일

#### ✅ Windows (.dll 파일 생성)
```sh
gcc -shared -o nativeLib.dll -I"%JAVA_HOME%\include" -I"%JAVA_HOME%\include\win32" JNIDemo.c
```

#### ✅ Linux/macOS (.so 파일 생성)
```sh
gcc -shared -o libnativeLib.so -fPIC -I${JAVA_HOME}/include -I${JAVA_HOME}/include/linux JNIDemo.c
```

---

### 3.5 실행
```sh
java JNIDemo
```
출력: `Result from C: 15`

---

## 4. JNI 데이터 변환

### 4.1 기본 타입 매핑
| Java 타입 | JNI 타입 | C/C++ 타입 |
|-----------|---------|------------|
| `boolean` | `jboolean` | `unsigned char` |
| `byte` | `jbyte` | `char` |
| `char` | `jchar` | `unsigned short` |
| `short` | `jshort` | `short` |
| `int` | `jint` | `int` |
| `long` | `jlong` | `long long` |
| `float` | `jfloat` | `float` |
| `double` | `jdouble` | `double` |

### 4.2 문자열 변환 (`jstring <-> C 문자열`)
```c
JNIEXPORT void JNICALL Java_JNIDemo_printString(JNIEnv *env, jobject obj, jstring message) {
const char *nativeStr = (*env)->GetStringUTFChars(env, message, 0);
printf("Received string: %s\n", nativeStr);
(*env)->ReleaseStringUTFChars(env, message, nativeStr);
}
```

---

## 5. JNI의 주요 함수

| 함수 | 설명 |
|------|------|
| `FindClass` | Java 클래스 찾기 |
| `GetMethodID` | 메서드 ID 가져오기 |
| `CallVoidMethod` | Java 메서드 호출 |
| `NewStringUTF` | C 문자열 → Java 문자열 변환 |
| `GetStringUTFChars` | Java 문자열 → C 문자열 변환 |
| `NewObject` | Java 객체 생성 |
| `DeleteLocalRef` | 로컬 참조 해제 |

---

## 6. JNI의 장점과 단점

### ✅ 장점
- C/C++ 네이티브 코드와 통합 가능
- 성능 최적화 가능
- OS API 및 하드웨어 접근 가능

### ❌ 단점
- 플랫폼 의존적 (OS마다 다른 바이너리 필요)
- 메모리 관리 필요 (GC의 영향을 받지 않음)
- 예외 처리가 복잡

---

## 7. 결론
JNI는 **Java와 네이티브 코드(C/C++)를 연결하는 강력한 도구**다.  
그러나, 플랫폼 의존성과 메모리 관리를 고려해야 하며,  
성능 최적화가 필요한 경우에 신중하게 사용해야 한다.