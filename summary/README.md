# 정리

## NAT (Network Address Translation)

- 제한된 수의 인터넷 IPv4의 문제를 해결하기 위해 개발된 것으로 특정 IP 대역을 다른 IP 대역으로 변환하는 역할을 한다.

- Private IP 대역에서 인터넷을 통한 외부 통신이 필요한 경우 NAT를 통해 Public IP 대역으로 변환되어 인터넷 통신을 가능케 한다.

### SNAT (Source Network Address Translation)

- Private IP 대역을 Public IP 대역으로 변환하는 것을 의미한다.

### DNAT (Destination Network Address Translation)

- Public IP 대역을 Private IP 대역으로 변환하는 것을 의미한다.

### Static NAT

- Private IP : Public IP = 1 : 1 인 상황으로 1:1로 맵핑한다.

- 대부분의 Public Cloud에서 Public IP를 부여할 때 이 방식이다.

### Dynamic NAT

- Private IP : Public IP = N : N 혹은 N : 1인 상황을 의미한다.

### VM에서의 NAT

- 하이퍼바이저가 설치된 컴퓨터와 연결된 라우터를 통한 외부와의 통신도 NAT에 해당되지만 VM을 생성할 때 그 VM이 가지는 네트워크 인터페이스의 한 종류인 NAT도 가상의 라우터를 통해 컴퓨터 내에서 생성되는 가상 네트워크 속 리소스가 외부와 통신할 수 있도록 똑같은 역할을 한다.

- 내부에서 NAT의 역할이 한번 있고 그 후 실제 라우터를 통해 NAT의 역할이 있은 후 트래픽이 외부로 나갈 수 있다.

#### 추후 IGW와 NAT GW와 비교하여 상세히 이 레포지토리 + 아키텍트 레포지토리에도 추가 정리 (정리할게 없을수도)

## DHCP (Dynamic Host Configuration Protocol)

- 동적 호스트 구성 프로토콜로 일정 대역의 호스트에 IP를 부여하는 프로토콜이다.

- 온 프레미스에서는 DHCP를 통해 실질적으로 서브넷이 형성된다고 할 수 있다.

- 구체적인 서브넷 형성은 ESXi의 가상 스위치를 생성해 연결하고 라우터를 통해 서브넷을 위한 가상 네트워크 인터페이스를 생성하는 것이지만 IP 대역을 설정하고 그 대역의 IP를 부여하는 것이 표면적으로는 호스트가 서브넷 내에 있도록 하는 것이므로 위와 같이 표현했다.

- `10.0.1.0/24`를 서브넷으로 하고 싶다면 `10.0.1.0~255`까지의 IP를 DHCP를 통해 호스트에 부여해서 서브넷을 형성하는 것이다.

### DHCP 동작 방식

- DHCP도 서버-클라이언트 구조로 동작한다.

- DHCP 서버는 호스트에 할당할 IP 주소와 서브넷 마스크, 기본 게이트웨이를 설정한다.

- 클라이언트는 DHCP 서버를 찾아 IP를 할당받기 위해 DHCP Discover (UDP/목적지 포트 번호 67번)를 Broadcast로 날린다.

- **_의문이 들었던 점은 어떻게 DHCP 서비스가 설치되지 않은 서버 인스턴스가 생성되자마자 DHCP Discover를 보낼까? 였는데 네트워크가 초기화될때 DHCP Discover를 Broadcast한다고 한다. 아마 위 내용은 DHCP 서비스를 통해서가 아니라 네트워크 서비스를 통해서인 것 같다._**

- DHCP 서버는 자신이 가진 IP 풀에서 할당되지 않은 IP를 ICMP로 확인한 다음 클라이언트에게 사용해도 좋을 네트워크 설정(MAC, IP, 서브넷 마스크, 게이트웨이, DNS, DHCP 서버의 네트워크 정보)의 제안을 DHCP Offer (UDP/목적지 포트 번호 68번)를 Broadcast 혹은 Unicast로 보낸다.

- DHCP Offer가 Broadcast일지 Unicast일지는 DHCP Discover의 메세지 내 Broadcast Flag의 값에 따라 달라지며 Broadcast Flag가 1이면 Broadcast로 보내고 0이면 Unicast로 보낸다.

- 일반적으로 Broadcast로 보내지며 이유는 단순한 논리로 아직 호스트의 IP를 모르기 때문이다.

- 클라이언트는 DHCP Offer를 수락하고 DHCP Request를 Broadcast로 보낸다.

- 서버가 Request에 대해 DHCP ACK을 클라이언트에 보내면 IP의 동적 할당이 완료된다.

- DHCP ACK도 DHCP Offer와 마찬가지로 Broadcast 혹은 Unicast로 보내진다. 각각의 이유는 DHCP Offer와 동일하다.

### 퍼블릭 클라우드의 DHCP 방식에 대해 추후 정리

## DNS (Domain Name System)

## 데이터스토어

## HAProxy

## vSphere
