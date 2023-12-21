

---
### previous

이전 글에서 대칭키와 비대칭키를 이용하여 서버 간 통신을 안전하게 하는 방법에 대해 알아보았다.
서버와 서버 간 대칭키를 공유하기 위해, 비대칭키를 이용하여 client가 server에 대칭키를 전송하며 그 과정은 다음과 같았다. (구글과의 통신 예시)

	1. client가 google server에 통신 요청
    2. google은 client로 부터 대칭키를 받기 위해 client에게 자신의 public key를 client에게 전송
    3. client는 서버로부터 넘겨받은 public key를 통해 자신의 대칭키를 암호화하여 server로 전송
    4. google server는 자신의 private 키로 대칭키를 복호화.

<br>

---
### 보안의 구멍

그러나 이와 같은 방식에는 한 가지 문제점이 있는데, 바로 중간자가 server의 **public key**를 가로채고 자신의 **public key**를 client에게 제공하여 client의 대칭키를 탈취할 수 있다는 점이다.

그 과정은 다음과 같다.

	1. client가 google server에 통신 요청
    2. google은 client로 부터 대칭키를 받기 위해 client에게 자신의 public key를 client에게 전송
    
    3. 이 때, 중간자가 개입하여 client로 향하는 google의 public key를 가로채고, 자신의 public key를 client에게 전송
    4. client는 서버로부터 넘겨받은 (줄 알았으나 사실은 중간자로부터 넘겨받은) public key를 통해 자신의 대칭키를 암호화하여 server로 전송
    5. 중간자는 다시 4번의 정보를 가로채서, 자신의 private key로 대칭키를 복호화하여 탈취.
    6. client 와 server 간의 통신이 계속 이뤄지도록 해야 하므로, 사용자의 대칭키를 google의 public key로 다시 암호화하여 google에 전송.
    
    7. google과 client는 중간자 개입이 있었는지 모르고 정상적으로 통신이 성공한 것 처럼 보이는 상태이다.
    8. 이후 client와 google이 주고받는 정보를 중간자는 계속 가로채면서 대칭키로 복호화하여 탈취

client와 google이 통신하며 대칭키를 공유하는 과정에서 중간자는 3~6의 과정을 통해 client의 대칭키를 탈취하고, 이를 통해 암호화된 정보를 복호화하여 사용자의 민감정보를 가로챌 수 있게 된다.

<br>

---
### 그래서 어떡하라고? 🫠

서버와의 안전한 통신을 위해 사용자와 서버는 비대칭키를 주고 받으며 대칭키를 공유하는 과정을 거쳤으나, 중간자 공격(MITM)으로 인해 무용지물이 되어버렸다.

![](https://velog.velcdn.com/images/hyungzin0309/post/5a47be27-c00b-47fc-a5d9-3b03cc17119b/image.png)

위와 같은 상황의 근본적인 문제는, 서버로부터 넘겨받는 **public key** 를 신뢰할 수 없다는 것이다. 서버로 부터 넘겨받은 **public key**가 진짜 server의 것인지, 중간자의 것인지 client는 알 방법이 없기 때문이다.

<br>

---
### 제 3자의 개입

결론부터 말하자면 그래서 필요한게 보안을 책임져줄 제 3자의 개입이며, 이를 통해 보안을 강화한 모델을 **PKI** (Public Key Infrastructure)라고 한다.

서버는 이제 client에게 달랑 자신의 **public key**만 보내지 않고, 기관(CA)으로 부터 발급받은 **인증서**를 client에게 전송한다.

인증서의 내용은 다음과 같다.

1. 인증서를 소유한 **Server의 정보** (+ 이외의 필요한 정보들)
2. server의 **public key**
3. CA 기관의 서명

<br>
인증서 발급부터 server와 client 간 통신의 과정은 다음과 같다.

	1. Server는 자신이 사용할 private key, public key 키 쌍을 생성한다.
    2. Server는 키페이 중 public key와 서버의 정보를 포함한 인증서 서명 요청(CSR)을 작성하여 CA에 인증서를 요청한다.
    3. CA에서는 Server로부터 넘겨받은 server 정보, public key를 이용해 인증서를 생성하고 CA의 서명 (인증서 내용의 hash + CA의 private key 를 이용한 암호화된 문자열)을 함께 담아 sercer에 제공한다.
    4. 이제 server는 server정보, 공개키, ca기관 서명이 담긴 인증서를 client에게 전송한다.
    5. client는 자신이 가지고 있는 CA의 public key를 통해 인증서가 유효한지, 인증서에 있는 server의 정보가 자신이 통신하고자 하는 server정보가 맞는지 확인할 수 있다.

이와 같은 과정을 통해 client는 요청 서버의 **public key**가 유효한 것인지 확인할 수 있다.

* 참고로 client는 browser 설치 시 browser에서 제공하는 CA public key를 사용하게 된다.
  <br>

---
### 상황 예시

google과의 통신을 예로 들어보자.

	1. 구글은 자신의 서버가 사용할 인증서를 CA로 부터 발급받는다.
    2. 인증서에는 google의 domain 정보, google의 public key, CA의 서명이 담겨있다.
    3. client가 google에 통신 요청 시, google은 client에게 자신의 인증서를 전송한다.
    4. client는 google로부터 넘겨받은 인증서의 정보를 보고 server 정보가 google이 맞는지, CA public key를 통해 인증서가 위변조되었는지 여부를 확인한다.
    5. 검증된 인증서임을 확인한 client는 자신의 대칭키를 google public key로 암호화하여 서버에 전송한다.

만약 위와 같이 인증서를 통해 google이 client에게 public key를 전송하는 상황에서 중간자가 개입하면 어떻게 될까?    
<br>

---
### 중간자 개입 상황 (1)


중간자는 client와 google 간에 인증서를 통해 통신한다는 점을 알고 자신도 CA에서 인증서를 발급받기로 한다.
대칭키 탈취를 위해 자신이 생성한 public key 와 private key중 public key 정보를 담은 인증서를 발급받는다. 하지만 CA의 엄격한 검수 때문에 google의 domain 정보가 담긴 인증서를 생성하지는 못하고, 임의의 domain 정보를 담은 인증서를 발급받았다.

1. google이 client로 인증서 전송
2. 중간자가 이를 가로채고 자신이 가진 인증서로 바꿔치기하여 client에게 전송한다.
3. client는 서버로부터 인증서를 전달받는다.
4. 인증서 검증을 위해 서명을 확인한다.
5. CA의 public key로 확인해본 결과, 해당 인증서는 CA로부터 정식으로 발급받은 유효한 인증서가 맞다.
6. 인증서를 소유한 Server의 정보를 확인한다.
7. 이게 웬걸;; Google이 아닌 Gagle 서버가 소유한 인증서이다.
8. client는 google로 부터 전송된 인증서가 아님을 알게되어 통신을 중단한다.

<br>

---
### 중간자 개입상황 (2)

(1) 의 방법으로 client를 속이기 실패한 중간자는

1. 자신의 CA인증서 정보에 있는 domain 정보를 google로 변경한다.
2. 가로챈 Google의 인증서에 있는 public key 정보를 자신의 것으로 바꿔치기한다.

와 같은 방법으로 사용자를 속이려 한다.

사용자는 인증서 검증을 위해 인증서의 서명을 CA public key로 복호화하고, 인증서의 내용을 해시한 결과와 비교한다.
비교값이 달라 사용자는 해당 인증서가 **위변조되었음을 감지**하고 통신을 중단한다.
