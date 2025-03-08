## API 종류 

> API(Application Programming Interface)는 소프트웨어 간의 상호작용을 가능하게 하는 인터페이스다. API는 다양한 형태로 제공되며, 사용 목적과 방식에 따라 여러 종류로 나뉜다. 대표적인 API의 종류와 특징을 정리해 본다.

---

### 1. **REST API (Representational State Transfer API)**
#### ✅ 특징
- **HTTP 프로토콜 기반**: GET, POST, PUT, DELETE 같은 HTTP 메서드를 활용해 리소스를 조작한다.
- **URI(Uniform Resource Identifier) 사용**: 특정 리소스를 식별하는데 URI를 활용한다.
- **JSON 또는 XML 데이터 형식 사용**: 일반적으로 JSON을 주로 사용하며 가독성이 좋고 경량이다.
- **Stateless(무상태성)**: 각 요청은 독립적으로 처리되며, 서버는 이전 요청 정보를 저장하지 않는다.

#### 🔹 사용 예시
- **SNS API**: 페이스북, 트위터, 인스타그램 API
- **클라우드 API**: AWS S3, Google Cloud Storage API

```java
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.net.URI;

public class RestApiExample {
    public static void main(String[] args) throws Exception {
        HttpClient client = HttpClient.newHttpClient();
        HttpRequest request = HttpRequest.newBuilder()
                                .uri(URI.create("https://jsonplaceholder.typicode.com/posts"))
                                .GET()
                                .build();

        HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());

        System.out.println("Response Code: " + response.statusCode());
        System.out.println("Response Body: " + response.body());
    }
}
```

---

### 2. **SOAP API (Simple Object Access Protocol API)**
#### ✅ 특징
- **XML 기반**: 데이터 전송을 XML 형식으로 처리하며 구조적이다.
- **엄격한 규칙과 보안**: 보안이 중요한 금융, 기업용 시스템에서 주로 사용된다.
- **WS-Security 지원**: 메시지 암호화, 인증 등의 보안 기능을 포함한다.
- **상태 유지 가능**: REST와 달리 상태를 유지할 수 있으며, 트랜잭션을 관리하기 용이하다.

#### 🔹 사용 예시
- **은행 및 금융 서비스**: 결제 게이트웨이, 계좌 이체 API
- **기업용 웹 서비스**: ERP(전사적 자원 관리) 시스템

```java
import java.io.OutputStream;
import java.net.HttpURLConnection;
import java.net.URL;

public class SoapApiExample {
    public static void main(String[] args) throws Exception {
        String url = "https://www.dataaccess.com/webservicesserver/NumberConversion.wso";
        String xmlData = """
                        <?xml version="1.0" encoding="utf-8"?>
                        <soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
                            <soap:Body>
                                <NumberToWords xmlns="http://www.dataaccess.com/webservicesserver/">
                                    <ubiNum>123</ubiNum>
                                </NumberToWords>
                            </soap:Body>
                        </soap:Envelope>
                        """;

        URL endpoint = new URL(url);
        HttpURLConnection connection = (HttpURLConnection) endpoint.openConnection();
        connection.setRequestMethod("POST");
        connection.setRequestProperty("Content-Type", "text/xml; charset=utf-8");
        connection.setDoOutput(true);

        try (OutputStream os = connection.getOutputStream()) {
            os.write(xmlData.getBytes());
            os.flush();
        }

        int responseCode = connection.getResponseCode();
        System.out.println("Response Code: " + responseCode);
    }
}
```

---

### 3. **GraphQL API**
#### ✅ 특징
- **쿼리 기반 데이터 요청**: 필요한 데이터만 요청할 수 있어 네트워크 효율성이 높다.
- **단일 엔드포인트 사용**: 여러 개의 엔드포인트를 사용하는 REST와 달리, 하나의 엔드포인트에서 다양한 데이터를 요청 가능하다.
- **데이터 중복 방지**: 필요한 데이터만 반환하므로 REST API보다 최적화된 응답을 받을 수 있다.

#### 🔹 사용 예시
- **Facebook API**: 페이스북에서 GraphQL을 개발하여 활용 중
- **GitHub API**: GraphQL을 활용한 데이터 요청 제공

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class GraphQLApiExample {
    public static void main(String[] args) throws Exception {
        String url = "https://countries.trevorblades.com/";
        String graphqlQuery = """
                                {
                                "query": "{ country(code: \\"KR\\") { name native capital currency } }"
                                }
                                """;

        HttpClient client = HttpClient.newHttpClient();
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create(url))
                .header("Content-Type", "application/json")
                .POST(HttpRequest.BodyPublishers.ofString(graphqlQuery))
                .build();

        HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());

        System.out.println("Response Code: " + response.statusCode());
        System.out.println("Response Body: " + response.body());
    }
}
```

---

### 4. **gRPC API (Google Remote Procedure Call API)**
#### ✅ 특징
- Restful API 보다 빠른 응답
  - **프로토콜 버퍼(Protocol Buffers) 사용**: Restful API 가 사용하는 JSON 보다 작은 바이너리 형태의 데이터를 사용하여 속도가 빠르다.
  - **HTTP/2 기반**: 스트리밍 및 멀티플렉싱 (하나의 TCP 연결을 통해 여러 요청 처리)을 지원하여 네트워크 효율이 높다.
- **다양한 언어 지원**: C++, Java, Python 등 다양한 언어에서 사용할 수 있다.
- **양방향 스트리밍 지원**: 서버와 클라이언트 간 실시간 데이터 통신이 가능하다.

#### 🔹 사용 예시
- **마이크로서비스 통신**: 내부 서비스 간 데이터 전송 (ex: Kubernetes 내부 서비스)
- **클라우드 서비스**: Google Cloud API

### **📌 1) `.proto` 파일 정의 (example.proto)**
```proto
syntax = "proto3";

service ExampleService {
    rpc GetUser (UserRequest) returns (UserResponse);
}

message UserRequest {
    string user_id = 1;
}

message UserResponse {
    string name = 1;
    int32 age = 2;
}
```

### **📌 2) 서버 구현 (ExampleServer.java)**
```java
import io.grpc.Server;
import io.grpc.ServerBuilder;
import io.grpc.stub.StreamObserver;

public class ExampleServer {
    public static void main(String[] args) throws Exception {
        Server server = ServerBuilder.forPort(50051)
                            .addService(new ExampleServiceImpl())
                            .build()
                            .start();

        System.out.println("gRPC Server started...");
        server.awaitTermination();
    }

    static class ExampleServiceImpl extends ExampleServiceGrpc.ExampleServiceImplBase {
        @Override
        public void getUser(UserRequest request, StreamObserver<UserResponse> responseObserver) {
            UserResponse response = UserResponse.newBuilder()
                    .setName("Alice")
                    .setAge(25)
                    .build();

            responseObserver.onNext(response);
            responseObserver.onCompleted();
        }
    }
}
```

### **📌 3) 클라이언트 구현 (ExampleClient.java)**
```java
import io.grpc.ManagedChannel;
import io.grpc.ManagedChannelBuilder;

public class ExampleClient {
    public static void main(String[] args) {
        ManagedChannel channel = ManagedChannelBuilder.forAddress("localhost", 50051)
                                    .usePlaintext()
                                    .build();

        ExampleServiceGrpc.ExampleServiceBlockingStub stub = ExampleServiceGrpc.newBlockingStub(channel);
        UserRequest request = UserRequest.newBuilder().setUserId("1234").build();
        UserResponse response = stub.getUser(request);

        System.out.println("Name: " + response.getName());
        System.out.println("Age: " + response.getAge());

        channel.shutdown();
    }
}
```

> 서버에서 구현된 자바 interface 를 포함한 라이브러리를 클라이언트측에서 가지고 있으면서 interface 의 메소드를 호출하면 내부적으로 http 등의 통신이 이뤄지는 형식.

---

### 5. **WebSocket API**
#### ✅ 특징
- **양방향 통신 지원**: 클라이언트와 서버가 실시간으로 데이터를 주고받을 수 있다.
- **지속적인 연결 유지**: REST API처럼 요청-응답 방식이 아니라, 연결을 유지하면서 데이터를 주고받는다.
- **낮은 지연시간(Low Latency)**: 실시간성이 중요한 서비스에서 활용된다.

#### 🔹 사용 예시
- **실시간 채팅**: WhatsApp, Slack, Discord API
- **온라인 게임**: 멀티플레이어 게임 서버 통신
- **금융 거래 시스템**: 실시간 주식 가격 조회

### **📌 1) WebSocket 서버 구현 (WebSocketServerExample.java)**

```java
import org.java_websocket.server.WebSocketServer;
import org.java_websocket.handshake.ClientHandshake;
import org.java_websocket.WebSocket;

import java.net.InetSocketAddress;

public class WebSocketServerExample extends WebSocketServer {
    
    public WebSocketServerExample(InetSocketAddress address) {
        super(address);
    }

    @Override
    public void onOpen(WebSocket conn, ClientHandshake handshake) {
        System.out.println("New connection: " + conn.getRemoteSocketAddress());
    }

    @Override
    public void onClose(WebSocket conn, int code, String reason, boolean remote) {
        System.out.println("Closed connection: " + conn.getRemoteSocketAddress());
    }

    @Override
    public void onMessage(WebSocket conn, String message) {
        System.out.println("Received: " + message);
        conn.send("Echo: " + message);
    }

    public static void main(String[] args) {
        WebSocketServer server = new WebSocketServerExample(new InetSocketAddress(8765));
        server.start();
        System.out.println("WebSocket Server started on port 8765");
    }
}
```

### **📌 2) WebSocket 클라이언트 구현 (WebSocketClientExample.java)**
```java
import org.java_websocket.client.WebSocketClient;
import org.java_websocket.handshake.ServerHandshake;

import java.net.URI;

public class WebSocketClientExample extends WebSocketClient {
    
    public WebSocketClientExample(URI serverUri) {
        super(serverUri);
    }

    @Override
    public void onOpen(ServerHandshake handshakedata) {
        System.out.println("Connected to WebSocket Server");
        send("Hello, WebSocket!");
    }

    @Override
    public void onMessage(String message) {
        System.out.println("Received: " + message);
    }

    public static void main(String[] args) throws Exception {
        WebSocketClientExample client = new WebSocketClientExample(new URI("ws://localhost:8765"));
        client.connectBlocking();
    }
}
```

---

### 6. **OpenAPI (Swagger)**
#### ✅ 특징
- **REST API 문서 자동 생성**: API 스펙을 문서화하고 시뮬레이션할 수 있다.
- **JSON/YAML 형식 사용**: API 요청 및 응답을 정의하는데 JSON 또는 YAML을 활용한다.
- **API 테스트 및 검증 가능**: API 호출을 직접 테스트할 수 있는 UI 제공

#### 🔹 사용 예시
- **API 개발 및 문서화**: Swagger UI를 활용한 API 설계 및 테스트

---

### 7. **Internal API (Private API)**
#### ✅ 특징
- **내부 시스템에서만 사용**: 외부 공개 없이 내부 서비스 간 통신을 위해 사용된다.
- **보안이 중요**: 기업 내부 시스템이므로 강력한 인증과 보안이 필요하다.
- **기업 내부 최적화**: 특정 비즈니스 로직에 맞게 최적화되어 있다.

#### 🔹 사용 예시
- **기업 내 마이크로서비스 API**: 사내 인트라넷 시스템
- **ERP 시스템 통신**: 회사 내부 시스템 연동

---

### 8. **Public API (Open API)**
#### ✅ 특징
- **누구나 접근 가능**: 개발자들이 자유롭게 활용할 수 있도록 공개된 API
- **무료 또는 유료 제공**: 일부 API는 무료로 제공되지만, 일정 요청 이상은 유료로 제공되기도 한다.
- **사용자 인증 필요**: 보안과 남용 방지를 위해 API 키, OAuth 등을 활용한 인증이 필요하다.

#### 🔹 사용 예시
- **Google Maps API**: 지도 데이터 제공
- **Twitter API**: 트윗 조회 및 게시 가능

---

### 9. **Composite API**
#### ✅ 특징
- **여러 API 호출을 하나의 요청으로 처리**: REST API가 여러 번 호출되어야 하는 경우를 줄여 성능을 개선한다.
- **연결된 서비스 실행 가능**: 여러 개의 마이크로서비스를 한 번의 요청으로 처리할 수 있다.
- **네트워크 비용 절감**: 여러 API 요청을 하나로 묶어 전송함으로써 네트워크 비용을 줄일 수 있다.

#### 🔹 사용 예시
- **마이크로서비스 아키텍처**: 여러 개의 서비스에서 데이터를 가져와 조합하여 응답

---

## **📌 API 종류별 비교 정리**

| API 종류       | 데이터 형식 | 상태 유지 | 주요 특징 | 대표적인 사용 사례 |
|--------------|-----------|----------|---------|-------------|
| **REST API** | JSON/XML  | ❌ 무상태 | HTTP 기반, URI 사용 | SNS API, 클라우드 서비스 |
| **SOAP API** | XML       | ⭕ 가능  | 높은 보안, WS-Security 지원 | 금융, 기업용 웹 서비스 |
| **GraphQL API** | JSON  | ❌ 무상태 | 필요한 데이터만 요청 | Facebook, GitHub API |
| **gRPC API** | 바이너리  | ⭕ 가능  | HTTP/2 기반, 고속 성능 | 마이크로서비스, 클라우드 |
| **WebSocket API** | JSON/바이너리 | ⭕ 가능  | 실시간 양방향 통신 | 채팅, 게임, 금융 거래 |
| **OpenAPI** | JSON/YAML | ❌ 무상태 | API 문서 자동화 | API 개발 및 테스트 |
| **Internal API** | JSON/XML  | ⭕ 가능  | 내부 시스템 전용 | 기업 내부 서비스 |
| **Public API** | JSON/XML  | ❌ 무상태 | 누구나 사용 가능 | 구글 맵, 트위터 API |
| **Composite API** | JSON/XML  | ⭕ 가능  | 여러 API 요청을 하나로 처리 | 마이크로서비스 통합 |

---

### **🔎 결론**
API는 용도와 환경에 따라 다양한 방식으로 제공된다. REST API가 가장 널리 사용되지만, GraphQL, gRPC, WebSocket 등 특정 상황에 적합한 API가 존재한다. 필요한 기능과 성능 요구사항을 고려하여 적절한 API를 선택하는 것이 중요하다.