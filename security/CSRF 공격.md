> CSRF(Cross-Site Request Forgery, 사이트 간 요청 위조) 공격은 사용자가 의도하지 않은 요청을 특정 웹 애플리케이션에 보내도록 속이는 공격 방식이다. 즉, 공격자는 사용자의 인증된 세션을 악용해 피해자가 원하지 않는 행동을 수행하도록 만든다.

## 1. CSRF 공격 개념
웹 애플리케이션에서 사용자가 로그인하면, 세션 쿠키 또는 토큰을 통해 인증된 상태가 유지된다. CSRF 공격자는 피해자가 인증된 상태에서 악의적인 요청을 보내도록 유도해 공격을 수행한다.

### 예제 시나리오
1. 사용자가 **A 웹사이트**(예: 인터넷 뱅킹)에 로그인하여 세션이 유지됨
2. 공격자가 악성 스크립트가 포함된 **B 웹사이트**를 운영함
3. 피해자가 B 웹사이트를 방문하면, 브라우저는 자동으로 A 웹사이트에 요청을 보냄
4. A 웹사이트는 피해자의 세션을 이용해 요청을 정상적인 사용자 요청으로 처리함
5. 피해자의 계좌에서 공격자의 계좌로 돈이 이체됨

CSRF(Cross-Site Request Forgery, 사이트 간 요청 위조) 공격은 피해자가 특정 웹사이트에 **로그인된 상태**에서 공격자가 의도한 **위조된 요청**을 서버에 보내도록 유도하는 방식이다. 공격자는 이를 통해 피해자의 계정으로 **비밀번호 변경, 송금, 게시글 작성** 등의 행위를 실행할 수 있다.

---

## 2. CSRF 공격이 이뤄지는 과정 (구체적인 시나리오)

### **1) 기본적인 CSRF 공격 시나리오**
**(예제: 인터넷 뱅킹에서 공격자가 돈을 송금하는 경우)**

### **🔹 ① 피해자가 인터넷 뱅킹에 로그인**
- 피해자가 **https://bank.com**에서 계좌에 로그인하면, 서버는 `session_id`라는 쿠키를 발급하여 사용자의 로그인 상태를 유지함.
- 이후 피해자가 다른 페이지를 방문하더라도, 브라우저는 `session_id` 쿠키를 자동으로 포함하여 요청을 보냄.

### **🔹 ② 공격자가 CSRF 공격 페이지를 제작**
- 공격자는 **공격용 웹사이트**(예: `https://evil.com`)를 만들고, 여기에 자동으로 특정 요청을 보내는 HTML 또는 JavaScript 코드를 삽입함.

```html
<!DOCTYPE html>
<html>
<head>
    <title>무료 이벤트 참여!</title>
</head>
<body>
    <h1>이벤트에 참여하면 100만원을 드립니다!</h1>
    <img src="https://bank.com/transfer?to=attacker&amount=1000000">
</body>
</html>
```

- 위 HTML 페이지에 포함된 `<img>` 태그는 **bank.com의 송금 API**를 호출하는 URL을 포함하고 있음.
- 피해자가 이 페이지를 방문하면, 브라우저는 자동으로 `bank.com`에 GET 요청을 보내게 됨.
- 브라우저는 보안 정책상 **동일 사이트 쿠키(SameSite=None)가 아닌 경우 세션 쿠키를 자동으로 포함**하여 요청을 보냄.

### **🔹 ③ 피해자가 공격자의 웹사이트 방문**
- 피해자가 `https://evil.com`을 방문하면 위의 `<img>` 태그가 자동으로 실행되며, **bank.com에 요청이 전송됨.**
- 요청 내용: `https://bank.com/transfer?to=attacker&amount=1000000`
- **중요한 점**: 피해자는 로그인된 상태이므로, **요청이 정상적인 사용자 요청처럼 보임**.

### **🔹 ④ 은행 서버에서 요청을 처리**
- `bank.com` 서버는 요청을 받은 후, 사용자의 로그인 세션이 유효하다고 판단하여 요청을 처리함.
- 결과적으로 피해자의 계좌에서 **공격자의 계좌로 100만원이 송금됨**.

---

### **2) POST 요청을 이용한 CSRF 공격**
어떤 사이트는 중요한 요청을 `GET`이 아닌 `POST` 방식으로 처리함. 하지만 CSRF 공격은 여전히 가능함.

### **🔹 ① 공격자가 자동으로 폼을 제출하는 웹페이지를 생성**
```html
<!DOCTYPE html>
<html>
<body onload="document.forms[0].submit();">
    <form action="https://bank.com/transfer" method="POST">
        <input type="hidden" name="to" value="attacker">
        <input type="hidden" name="amount" value="1000000">
    </form>
</body>
</html>
```
- `<body onload="document.forms[0].submit();">`를 이용해 페이지가 열리자마자 폼을 자동 제출함.
- 사용자가 `evil.com`을 방문하는 순간 공격이 실행됨.

---

## 3. CSRF 공격이 성공하는 이유
1. **웹 브라우저가 자동으로 쿠키를 포함**하여 요청을 보내기 때문
2. **서버가 요청의 출처를 확인하지 않기 때문**
3. **사용자가 공격자가 유도한 페이지를 방문하기만 해도 공격이 가능**하기 때문

CSRF 공격은 XSS(크로스 사이트 스크립팅)처럼 직접적인 코드 실행이 필요하지 않으며, 단순히 사용자를 속여 특정 페이지를 방문하도록 만들면 성공할 수 있다.

---

## 4. CSRF 공격을 방어하는 방법
### ✅ **(1) CSRF 토큰 사용**
- 서버는 사용자의 요청마다 난수 값을 포함한 CSRF 토큰을 발급
- 클라이언트는 요청 시 CSRF 토큰을 함께 전송해야 요청이 유효함
- 공격자는 토큰 값을 알 수 없으므로 위조 요청이 차단됨

```html
<form action="/transfer" method="POST">
    <input type="hidden" name="csrf_token" value="123456789">
    <input type="text" name="to">
    <input type="text" name="amount">
    <input type="submit">
</form>
```
### ✅ (2) SameSite 속성 설정
- `SameSite` 쿠키 속성을 `Strict` 또는 `Lax`로 설정하면, CSRF 공격을 방어할 수 있음.
- `Strict`: 모든 크로스 사이트 요청에서 쿠키가 전송되지 않음.
- `Lax`: 안전한 요청(GET)에서는 쿠키가 전송될 수 있음.

```http
Set-Cookie: session_id=abcd1234; Secure; HttpOnly; SameSite=Strict
```

- **Strict**: 완전히 다른 사이트에서 요청할 경우, 쿠키가 전송되지 않음.
- **Lax**: GET 요청에 한해서만 쿠키가 전송됨.

### ✅ **(3) Referrer 검사**
- 요청의 `Referer` 헤더를 확인하여 출처가 신뢰할 수 있는 도메인인지 확인.

### ✅ **(4) CORS 정책 강화**
- 신뢰할 수 있는 출처(origin)에서만 요청을 받을 수 있도록 설정.

---

## 3. CSRF 공격 방어 방법
### (1) CSRF 토큰 사용
- 서버는 사용자의 요청마다 난수 값을 포함한 CSRF 토큰을 발급
- 클라이언트는 요청 시 CSRF 토큰을 함께 전송해야 요청이 유효함
- 공격자는 토큰 값을 알 수 없으므로 위조 요청이 차단됨

```html
<form action="/transfer" method="POST">
    <input type="hidden" name="csrf_token" value="랜덤값1234">
    <input type="text" name="to">
    <input type="text" name="amount">
    <input type="submit">
</form>
```

### (2) SameSite 속성 설정
- `SameSite` 쿠키 속성을 `Strict` 또는 `Lax`로 설정하면, CSRF 공격을 방어할 수 있음.
- `Strict`: 모든 크로스 사이트 요청에서 쿠키가 전송되지 않음.
- `Lax`: 안전한 요청(GET)에서는 쿠키가 전송될 수 있음.

```http
Set-Cookie: session_id=abcd1234; Secure; HttpOnly; SameSite=Strict
```

### (3) Referrer 체크
- 요청의 `Referer` 헤더를 확인하여 신뢰할 수 없는 사이트에서 온 요청을 차단

### (4) CORS 정책 강화
- CORS 정책을 적절히 설정하여 신뢰할 수 없는 출처에서의 요청을 차단

