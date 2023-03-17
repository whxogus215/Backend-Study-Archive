# Contents Of Http
- [IP 프로토콜 그리고 TCP/UDP](#IP-프로토콜-그리고-TCP/UDP)
- [PORT 그리고 DNS](#PORT)
- 통신

###### Reference
- **(main)** 인프런 김영한 HTTP 강좌 : https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/dashboard


## IP 프로토콜 그리고 TCP/UDP
클라이언트와 서버는 인터넷을 통해 데이터를 전달한다. 이 때 출발지와 목적지를 정의하는 방법은 기기마다 고유의 IP 주소를 갖는 것이다. 이 때 데이터를 **패킷**이라는 단위로 묶어 보낸다. 하지만 데이터는 전송되는 과정에서 다양한 계층을 거치고 여러 기기들을 거치게 된다. (특히, 거리가 멀수록) 따라서 이 때, 패킷이 목적지에 잘 전달될 수 있도록 거치는 지점마다 목적지를 잘 설정할 필요가 있다. 이를 위해 **TCP와 UDP**가 등장하였다.  
### TCP/UDP
![image](https://user-images.githubusercontent.com/70999462/225890592-ecbbb1cd-ac2a-451f-9210-b6b5a9b4b17b.png)
> [인프런 김영한 HTTP 강좌](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/dashboard)

**TCP(Transmission Control Protocol)** 는 어플리케이션 계층으로부터 전달된 데이터에 TCP 정보를 생성하여 감싼다. 여기엔 출발지 PORT, 목적지 PORT, 전송 제어, 순서, 검증 정보 등등이 담겨있다. TCP의 주요 특징 중 하나는 출발지와 목적지 간 논리적 연결을 확인하는 것이다. 이를 **3 way handshake** 라고 한다. 또한 TCP는 데이터가 전송되었을 때 제대로 수신되었음을 알려주는 일종의 회신 기능이 있다. 그리고 여러 패킷이 순서대로 잘 전달될 수 있도록 하는 순서 보장 기능이 있다. (만약 세 번째 패킷이 두 번째 패킷보다 먼저 도착하면, 두 번째 패킷부터 다시 보내도록 한다.)  
**UDP(User Datagram Protocol)** 은 TCP와 같은 계층에 있으나, 앞서 말한 기능들이 들어있지 않은 백지에 가까운 상태이다. PORT 및 체크섬 정도의 기능만 있을 뿐 데이터의 전달 순서를 보장하지 않는다. 하지만 그만큼 단순하게 구현되어 있으며 속도가 빠르다. 이미 현실 인터넷 프로토콜에서는 TCP가 90퍼에 가까운 점유율을 보유하고 있으나, 사용자가 어플리케이션을 통해 UDP에 최적화된 기능들을 구현할 수 있다.
## PORT
PORT는 하나의 IP 주소, 즉 하나의 기기 안에서 동작하는 여러 프로세스를 구분하기 위한 위치정보이다. 컴퓨터는 여러 어플리케이션을 사용할 수 있기 때문에, 여러 서버로부터 데이터를 받게 된다. 따라서 데이터들을 구분하기 위한 정보가 필요하고, 이를 PORT라고 한다. IP가 아파트라면, PORT는 아파트 내에 있는 호수 번호라고 할 수 있다. TCP/IP 패킷에는 출발지,목적지의 IP 뿐만 아니라 PORT 번호까지 담고 있기 때문에 알맞은 PORT로 데이터를 전달할 수 있다.
  - 0 ~ 65535 : 할당 가능
    - 0 ~ 1023 : 잘 알려진 포트, 이 범위에 있는 PORT는 사용하지 않는 것을 권장
    - FTP : 20,21
    - TELNET : 23
    - HTTP : 80
    - HTTPS : 443

## DNS
![image](https://user-images.githubusercontent.com/70999462/225898186-914c7b04-3e9d-4fe4-89ed-278a0fb35e2b.png)
> [인프런 김영한 HTTP 강좌](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/dashboard)
**DNS(Domain Name System)** 는 일련의 숫자로 구성되어 외우기 어려운 IP 주소를 대신해 알기 쉬운 직관적인 이름을 부여하는 방법이다. 사용자가 접속하고자 하는 서버의 IP 주소를 모르더라도 DNS를 통해 연결할 수 있다. 또한 서버의 IP 주소가 변경되면 변경된 주소를 도메인과 연결시키면, 사용자는 IP 주소의 변경과 상관없이 동일하게 접속이 가능하다.


