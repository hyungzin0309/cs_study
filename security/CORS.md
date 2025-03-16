## CORS
> CORS(Cross-origin resource sharing, 크로스-오리진 리소스 공유)는 **웹 브라우저가 서로 다른 출처(Origin)에서의 요청을 제한하는 Same-Origin Policy(SOP)를 우회할 수 있도록 허용하는 보안 정책**이다.
<br>즉, **기본적으로 웹 브라우저는 다른 도메인의 리소스 요청을 차단하지만, CORS 정책을 통해 특정 조건에서 이를 허용할 수 있다.**

---

## 1. Same-Origin Policy(SOP)란?
**Same-Origin Policy(동일 출처 정책)** 은 보안상의 이유로 **다른 출처(origin)에서의 리소스 요청을 차단하는 정책**이다.

### ✅ **Same-Origin의 정의**
웹 페이지의 출처(Origin)는 다음 3가지 요소로 정의됨. <br>  
-> 프로토콜 + 호스트(도메인) + 포트
- **같은 출처(Same-Origin)**:
    - `https://example.com:443/page.html`
    - `https://example.com:443/api/data`
    - → 프로토콜, 도메인, 포트가 동일하면 같은 출처

- **다른 출처(Cross-Origin)**:
    - `http://example.com:80/api/data`  → **(프로토콜이 다름)**
    - `https://sub.example.com/api/data`  → **(서브도메인이 다름)**
    - `https://example.com:8080/api/data`  → **(포트가 다름)**

### ✅ **Same-Origin Policy가 차단하는 요청 예시**
```javascript
fetch("https://api.external-site.com/data")
    .then(response => response.json())
    .then(data => console.log(data));
````
위 코드는 `https://api.external-site.com`에서 데이터를 가져오려고 하지만, 브라우저는 해당 요청이 "다른 출처"에서 발생했기 때문에 기본적으로 차단함.

---

## 2. CORS란?
CORS(Cross-Origin Resource Sharing)는 **서버가 다른 출처에서 오는 요청을 허용할 수 있도록 설정하는 방식**이다.  
즉, **CORS를 설정하면 특정 도메인에서 오는 요청을 허용할 수 있다.**

---

## 3. CORS 요청의 유형
CORS 요청은 크게 **Simple Request(단순 요청)**과 **Preflight Request(사전 요청)** 두 가지로 나뉜다.

### ✅ (1) Simple Request(단순 요청)
**아래 조건을 만족하는 요청은 단순 요청으로 처리되며, 별도의 Preflight 요청 없이 바로 요청을 보낼 수 있다.**

### **🔹 단순 요청의 조건**
1. **허용된 HTTP 메서드**: `GET`, `POST`, `HEAD` 만 가능
2. **허용된 헤더**:
    - `Accept`
    - `Accept-Language`
    - `Content-Language`
    - `Content-Type` (`application/x-www-form-urlencoded`, `multipart/form-data`, `text/plain` 만 가능)
3. **XMLHttpRequest나 fetch 요청에서 `credentials: include`를 사용하지 않음**

### **🔹 Simple Request 예제**
```javascript
fetch("https://api.example.com/data")  // GET 요청 (단순 요청)
    .then(response => response.json())
    .then(data => console.log(data));
```
서버에서 `Access-Control-Allow-Origin: *` 또는 특정 도메인을 허용하면 요청이 성공함.

---

### ✅ (2) Preflight Request(사전 요청)
**위의 "단순 요청 조건"을 만족하지 않는 경우, 브라우저는 요청을 보내기 전에 `OPTIONS` 메서드를 이용해 서버에 미리 허용 여부를 확인하는 "사전 요청"을 보냄.**

### **🔹 Preflight 요청이 발생하는 경우**
- `PUT`, `DELETE`, `PATCH` 등의 메서드 사용
- `Authorization` 등의 추가적인 헤더 포함
- `application/json` 등의 Content-Type 사용
- `credentials: include` 옵션 사용

### **🔹 Preflight 요청 과정**
1. 클라이언트가 먼저 `OPTIONS` 메서드로 사전 요청을 보냄.
2. 서버가 `Access-Control-Allow-Methods`, `Access-Control-Allow-Headers` 등의 응답 헤더를 반환하여 허용 여부를 알려줌.
3. 클라이언트가 본 요청을 실제로 보냄.

### **🔹 Preflight 요청 예제**
```javascript
fetch("https://api.example.com/data", {
    method: "DELETE",
    headers: {
        "Authorization": "Bearer token123",
        "Content-Type": "application/json"
    }
})
.then(response => response.json())
.then(data => console.log(data));
```
위 요청은 `DELETE` 메서드를 사용하고 `Authorization` 헤더를 포함하기 때문에 **사전 요청(Preflight Request)**이 발생함.

---

## 4. CORS 응답 헤더 (서버 설정)
CORS 요청이 성공하려면, **서버에서 적절한 응답 헤더를 반환해야 함**.

### ✅ (1) `Access-Control-Allow-Origin`
특정 출처에서의 요청을 허용하는 헤더  
```http
Access-Control-Allow-Origin: https://example.com
```
- 특정 도메인(`https://example.com`)에서의 요청만 허용
- **`*`을 사용하면 모든 출처에서의 요청을 허용** (`Access-Control-Allow-Origin: *`)
- `credentials: include` 옵션을 사용하면 `*`을 사용할 수 없음.

---

### ✅ (2) `Access-Control-Allow-Methods`
허용할 HTTP 메서드 지정  
```http
Access-Control-Allow-Methods: GET, POST, PUT, DELETE
```

---

### ✅ (3) `Access-Control-Allow-Headers`
허용할 요청 헤더 지정  
```http
Access-Control-Allow-Headers: Content-Type, Authorization
```

---

### ✅ (4) `Access-Control-Allow-Credentials`
쿠키를 포함한 요청을 허용할지 여부 설정 (`true` 설정 시, `*` 사용 불가)  
```http
Access-Control-Allow-Credentials: true
```

---

### ✅ (5) `Access-Control-Max-Age`
Preflight 요청의 결과를 캐싱하는 시간(초 단위)  
```http
Access-Control-Max-Age: 3600
```
- **이 값을 설정하면 브라우저가 일정 시간 동안 Preflight 요청을 생략하고 캐시된 응답을 사용함.**

---

## 5. CORS 설정 예제 (서버 측)

### ✅ **(1) Express.js에서 CORS 설정**
```javascript
const express = require("express");
const cors = require("cors");
const app = express();

app.use(cors(
    {
        origin: "https://example.com",  // 특정 도메인 허용
        methods: ["GET", "POST", "PUT", "DELETE"],
        allowedHeaders: ["Content-Type", "Authorization"],
        credentials: true
}));

app.get("/data", (req, res) => {
    res.json({ message: "CORS 설정 완료!" });
});

app.listen(3000, () => console.log("서버 실행 중..."));
```

---

### ✅ **(2) Nginx에서 CORS 설정**
```nginx
server {
    listen 80;
    server_name api.example.com;

    location / {
        add_header 'Access-Control-Allow-Origin' 'https://example.com';
        add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS';
        add_header 'Access-Control-Allow-Headers' 'Content-Type, Authorization';
        add_header 'Access-Control-Allow-Credentials' 'true';
        if ($request_method = 'OPTIONS') {
            return 204;
        }
    }
}
```

---

# 6. 결론
✅ **Same-Origin Policy는 보안을 위해 크로스 오리진 요청을 차단하지만, CORS를 통해 예외적으로 허용할 수 있음.**  
✅ **Simple Request는 사전 요청 없이 바로 실행되지만, Preflight Request는 OPTIONS 요청을 먼저 보냄.**  
✅ **서버에서 적절한 CORS 응답 헤더(`Access-Control-Allow-Origin`, `Access-Control-Allow-Methods`, `Access-Control-Allow-Headers`)를 설정해야 허용됨.**  
✅ **쿠키를 포함한 요청을 하려면 `credentials: true`와 `Access-Control-Allow-Credentials: true`를 설정해야 함.**  