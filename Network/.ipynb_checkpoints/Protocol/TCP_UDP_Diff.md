# TCP UDP 차이

[TCP와 UDP의 특징과 차이](https://mangkyu.tistory.com/15)  
[TCP 3-way handshaking과 4-way handshaking](https://gmlwjd9405.github.io/2018/09/19/tcp-connection.html)  
[TCP 와 UDP 차이를 자세히 알아보자](https://velog.io/@hidaehyunlee/TCP-%EC%99%80-UDP-%EC%9D%98-%EC%B0%A8%EC%9D%B4#tcp-connection-3-way-handshake)  
[TCP와 UDP 차이 그리고 TCP/IP](https://madplay.github.io/post/network-tcp-udp-tcpip)

## TCP
* 네트워크 계층 중 전송 계층에서 사용하는 프로토콜
* 장치 사이에 논리적인 연결을 설정하여 신뢰성을 보장
* 네트워크에 연결된 프로그램 간에 일련의 옥텟을 안정적으로, 순서대로, 에러없이 교환할 수 있게 함

### 전송방식
1. 3-way handshake 연결 성립
   1. 클라이언트에서 서버에 접속을 요청하는 SYN 패킷 전송. 클라이언트는 SYN/ACK 응답을 기다리는 SYN_AWAIT 상태.
   2. 서버로부터 SYN 패킷을 받은 클라이언트는 요청을 수락하는 ACK와 SYN Flag 전송. 서버는 SYN_RECEIVED 상태.
   3. 클라이언트는 서버에 ACK 패킷을 보냄. 데이터 송수신 연결 성립.
2. 데이터 전송
3. 4-way handsahke 연결 종료
   1. 클라이언트에서 서버로 FIN 플래그 전송.
   2. 서버는 확인 메시지 전송. 통신이 끝날때까지 대기. -> TIME_AWAIT
   3. 서버는 통신이 끝났음을 확인하고 클라이언트에 FIN 패킷 전송.
   4. 클라이언트는 서버에 확인 메시지 전송.

데이터 전송이 완료되기 전에 FIN 플래그 전송 후 세션이 종료되는 경우 데이터가 손실됨.  
이런 손실을 방지하기 위해 기본 대기시간 TIME_AWAIT 설정.


### 흐름제어(Flow Control)
* 데이터를 송신하는 곳과 수신하는 곳의 데이터 처리 속도를 조절하여 수신자의 버퍼 오버플로우를 방지함.
* 송신하는 곳에서 감당이 안되게 많은 데이터를 빠르게 보내 수신하는 곳에서 문제가 일어나는 것을 막음.

### 혼잡제어 (Congestion Control)
* 네트워크 내의 패킷 수가 넘치게 증가하지 않도록 방지하는 것
* 정보의 소통량이 과다하면 패킷을 조금만 전송하여 혼잡 붕괴 현상이 일어나는 것을 막는다.

\* 참고자료  
[TCP congestion control / flow control 관련 질문](https://www.netmanias.com/ko/post/qna/5688)  
[TCP의 기능 중 Congestion Control, Flow Control](https://m.blog.naver.com/PostView.nhn?blogId=sanghun0318&logNo=220414053686&proxyReferer=https:%2F%2Fwww.google.com%2F)

### 그 외
* 높은 신뢰성 보장
* UDP보다 느린 속도
* 전이중(Full-Duplex), 점대점(Point-to-Point) 방식
  * 1:1 연결
  * 양방향 연결
  * 멀티캐스트, 브로드캐스트 지원 X


## UDP

* 비연결형 프로토콜. 논리적인 경로 X
* 패킷이 다른 경로로 전송되어 독립적인 관계를 지님.

### 전송방식
* 데이터그램 방식으로 통신.(패킷을 독립적으로 전송할 때 데이터그램이라 부름)
* 정보를 주고 받을 때 정보를 보내거나 받는다는 신호절차를 거치지 않음.
* UDP 헤더의 CheckSum 필드를 통해 최소한의 오류만 검출.

### 그 외
* 신뢰성이 낮음.
* TCP보다 속도가 빠름.
* 유저가 데이터를 손실 없이 받았는지 신경쓰지 않고 계속 보냄. 재전송 X
* 1:1 or 1:N or N:N 통신 가능.
* 흐름제어 X
  
위 같은 특징에 맞게 스트리밍처럼 성능 우선 서비스에 사용됨. 

## 비교

|프로토콜 종류|TCP|UDP|
|:-:|:---:|:---:|
|연결 방식|연결형 서비스|비연결형 서비스|
|패킷 교환 방식|가상 회선 방식|데이터그램 형식|
|전송 순서|전송 순서 보장|전송 순서가 바뀔 수 있음|
|수신 여부 확인|O|X|
|통신 방식|1:1|1:1 or 1:N or N:N|
|신뢰성|높음|낮음|
|속도|느림|빠름|