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

- **_강사님의 답변은 시스템에서 DHCP로 할당이 설정되어 있다면 그러니까 VM이면 VM 네트워크 설정에서 DHCP가 설정되어 있다면 서버가 생성될때 자동으로 255 Broadcast를 보내도록 되어있고 서버 인스턴스 내부의 주체는 생각할 필요가 없다는 것이였다._**

- DHCP 서버는 자신이 가진 IP 풀에서 할당되지 않은 IP를 ICMP로 확인한 다음 클라이언트에게 사용해도 좋을 네트워크 설정(MAC, IP, 서브넷 마스크, 게이트웨이, DNS, DHCP 서버의 네트워크 정보)의 제안을 DHCP Offer (UDP/목적지 포트 번호 68번)를 Broadcast 혹은 Unicast로 보낸다.

- DHCP Offer가 Broadcast일지 Unicast일지는 DHCP Discover의 메세지 내 Broadcast Flag의 값에 따라 달라지며 Broadcast Flag가 1이면 Broadcast로 보내고 0이면 Unicast로 보낸다.

- 일반적으로 Broadcast로 보내지며 이유는 단순한 논리로 아직 호스트의 IP를 모르기 때문이다.

- 클라이언트는 DHCP Offer를 수락하고 DHCP Request를 Broadcast로 보낸다.

- 서버가 Request에 대해 DHCP ACK을 클라이언트에 보내면 IP의 동적 할당이 완료된다.

- DHCP ACK도 DHCP Offer와 마찬가지로 Broadcast 혹은 Unicast로 보내진다. 각각의 이유는 DHCP Offer와 동일하다.

### 퍼블릭 클라우드의 DHCP 방식에 대해 추후 정리

## DNS (Domain Name System)

- 도메인 네임 시스템은 호스트의 IP 주소를 도메인 주소로 변경하고 도메인 주소를 IP 주소로 변경하기 위해 고안되었다.

### DNS 구성 요소

#### Domain Name Space

- 도메인 네임은 트리 형태로 계층 구조를 가지고 있다.

- 이 규칙 자체를 의미하는 말로 루트 서버가 TLD 서버에 대한 정보를 가지고 있고 TLD 서버가 2차 도메인 서버에 대한 정보를 가지고 있도록 해서 계층 구조를 형성해 분산시키는 규칙이다.

#### Name Server

- DNS 서버 그 자체를 의미하며 리눅스에선 bind, 윈도우에선 MS DNS 를 사용해서 구축된 서버이며 도메인 네임에 대한 쿼리에 IP 주소를 응답한다.

#### Resolver

- 사용자의 DNS 쿼리 요청을 받아 다른 네임 서버들과 통신하는 역할을 하는 서버로 DNS 전체 동작에서 클라이언트의 역할을 한다.

- Local DNS(기지국 서버), Recursive DNS Server, Recursor와 동의어로 볼 수 있다. 이후 DNS 동작 방식에서 후술

### DNS 동작 방식

- 사용자가 www.naver.com을 검색하면 Local DNS가 그 요청을 받는다.

- Local DNS는 우선 자신에게 캐싱되어 있는지 확인하고 있다면 바로 응답을 보내고 없다면 루트 도메인에 쿼리를 보낸다.

- 루트 도메인에서 .com Top Level Domain에 대한 정보를 Local DNS로 보내고 Local DNS는 그 TLD에 다시 쿼리를 보낸다.

- TLD는 naver.com 2차 도메인에 대한 정보를 Local DNS로 보내고 Local DNS는 다시 그 2차 도메인 서버에 쿼리를 보낸다.

- 2차 도메인 서버는 여기서 네이버에서 구축한 네임 서버 혹은 호스팅하는 도메인 업체의 네임 서버로 Local DNS에 서브 도메인을 포함한 www.naver.com의 정보를 가지고 있고 그 도메인의 IP를 Local DNS로 보낸다.

- 이제 Local DNS가 사용자에게 IP를 알려주고 사용자는 IP를 통해 HTTP, HTTPS 요청을 보낸다.

## vSphere

- vSphere는 엔터프라이즈급의 가상화 플랫폼이다.

- vSphere는 그 자체로 하나의 소프트웨어를 의미하는 것은 아니고 ESXi, vCenter와 같은 기술들을 통칭하는 의미이다.

### ESXi

- ESXi는 VMware에서 개발한 Type 1 Hypervisor이다.

- 타입 1 하이퍼바이저이기 때문에 OS 없이 베어메탈로 설치될 수 있으며 리소스 가상화와 관리에 용이하다.

### vCenter

- vCenter는 중앙 집중식 관리 플랫폼으로 다수의 ESXi, 그 안에 포함되는 모든 리소스를 중앙 관리하기 위한 소프트웨어이다.

- vMotion을 통해 ESXi간 마이그레이션을 지원하고 고가용성을 위한 HA와 내결함성 FT를 제공한다.

- vMotion은 마이그레이션을 하는 동안 패킷 손실이 거의 없다는 장점이 있다.

- DRS (Distributed Resource Scheduler) 기능을 통해 한 클러스터(한 ESXi)에 리소스가 몰리고 다른 클러스터의 리소스는 남는 상황이라면 자동으로 마이그레이션을 해서 분산을 시키는 기능이다.

- DRS 임계값마다 DRS 작동 민감도가 다르다.(1~5단계로 나뉜다.)

- 고가용성을 위해 HA 기능을 지원한다. HA는 ESXi 1 클러스터와 ESXi 2 클러스터가 있다고 가정했을때 ESXi 1에 생성된 서버가 ESXi 1이 갑자기 종료된 경우 ESXi 2로 마이그레이션되어 배포 상태가 유지되도록 하는 기능이다.

- 장애가 발생한 클러스터의 VM들이 대체 클러스터에서 실행될 수 있도록 하는 것이다.

- FT(Fault Tolerance) 기능은 HA에서 발생하는 패킷 손실을 방지하고 더 빠른 재해 복구를 위해 고안되었다.

- 모든 ESXi에 대해 적용할 수는 없고 제한이 있다.

- VM을 생성하면 그 VM에 대한 Stand By VM이 생성되는데 정확한 명칭은 FT에서는 Active VM과 Hot-Stand By VM으로 나뉘며 더 적극적인 대체 VM이라는 표현으로 보인다.

- Active VM의 정보가 Hot-Stand By VM에 지속적으로 동기화되며 Active VM에 문제가 생기면 Hot-Stand By가 Active VM이 되어 배포 상태를 유지한다.

## RFC 1918

- Private IP 대역, 사설망에 대한 IP 주소 범위를 정한 규칙이다.

- `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`의 대역이다.

### 사설망 (Private Network)

- 사설망은 위 사설망 대역에 해당하는 IP 주소 공간을 이용하는 네트워크로 가정, 기업의 내부 네트워크 등에서 쓰인다.

- 온프레미스나 퍼블릭 클라우드 구분없이 실제 서비스도 사설 네트워크 대역에 생성되어 NAT GW, IGW, 라우터 등을 통해 외부망, 인터넷과 통신을 한다.

## Cloud의 종류

### On Premises

- 기업이 자체 시설에서 인프라를 직접 보유하고 유지, 관리하는 방식을 의미한다.

- 단어 자체의 의미는 premises가 사업체의 부지 혹은 구내를 의미하고 On은 전원 On을 의미한다.

- 구내에 IT 시스템이 전원 On 되어있다. 전산 시스템을 구비하고 있다는 의미이다.

- Off Premises == 그 외 시스템 인프라로 곧 클라우드를 의미한다.

### Private Cloud

- Private Cloud라고 반드시 데이터 센터를 보유하고 자체 관리를 하는 것은 아니며 구축과 유지, 보수를 다른 인력에 맡길 수도 있다.

- CSP에도 Private Cloud의 호스팅을 맡길 수 있다.

#### On Premises vs Private Cloud

- 클라우드인지 아닌지에 따라 사용의 측면에서 차이가 발생하는데 Private Cloud가 더 확장성이 뛰어나고 유연한 활용이 가능해진다.

- 기업이 자체적으로 리소스의 제어권을 가진다는 측면에서는 동일하다.

- Private Cloud는 반드시 모든 인프라를 기업이 소유해야 하는 것은 아니기 때문에 사용되는 기술의 차이도 있지만 리소스를 누가 관리를 하는 지에 따라 구분할 수도 있다.

### Hybrid Cloud

- Split-tier 아키텍처로 DB를 Private Cloud에 나머지 WS, WAS 등의 서버는 Public Cloud에 하는 등의 방식이 가능하며 지금 가장 각광받는 클라우드 형태이다.

### Multi Cloud

- 복수의 Public Cloud에 인프라를 구축하고 서로 연동되도록 하는 형태이다.
