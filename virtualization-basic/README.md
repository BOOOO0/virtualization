# 가상화 기술의 이해

## 가상화 종류

### 전가상화 (Full-Virtualization)

- 게스트 OS에서 실행된 명령이 수행되기 위해 Binary Translation을 거치고 하이퍼바이저의 중재를 통해 하드웨어로 전달됩니다. 이 Binary Translation의 과정에서 지연이 발생합니다.

- Binary Translation을 하는 이유는 OS 마다 명령의 표현 방식이 다를 경우 (윈도우는 Add, 리눅스는 ADD와 같이 다를 경우) 이것을 번역해서 수행해야 하기 때문입니다.

### 반가상화 (Para-Virtualization)

- 사용자가 커널을 수정해서 각 OS 별로 번역 장치를 세팅해야 하는 성능은 좋고 커널 수정이 필요한(이게 하이퍼콜) 단점 윈도우 서버의 경우 커널이 오픈되어 있지 않아 더 어렵다

### 하드웨어 지원 가상화 (Hardware-assisted Virtualization) (HVM)

- 현 퍼블릭 클라우드가 사용하는 방식으로 전가상화의 일종인데 Binary Translation을 생략한다.

- 번역 장치를 CPU가 대신한다. BIOS에서 설정하는 그 가상화

## 하이퍼바이저

- 가상화를 구현해주는 소프트웨어

### Type1 하이퍼바이저 (네이티브 하이퍼바이저)

- 네이티브, 베어메탈의 의미 - 아무것도 설치되지 않은 컴퓨터에 설치되고 실행되는 하이퍼바이저

- 하이퍼바이저가 운영체제를 대신해서 설치되고 내가 이해한건 WSL을 사용하려고 Hyper-V를 사용했을 때 기존 윈도우도 하나의 가상 머신 OS가 되는 것이고 리눅스 우분투도 하나의 가상 머신 OS가 되어 사용할 수 있게 되는 것

- XenServer가 한 예로 AWS가 사용하고 있음 AWS는 컴퓨팅 자원에 XenServer 설치하여 가상화한 자원을 제공하는것

- ESXi(vSphere) - 고가의 기술

- Hyper-V - WSL에 사용

- KVM으로 넘어가는 추세

- 실무 용도

### Type2 하이퍼바이저 (호스티드 하이퍼바이저)

- 호스트 OS가 존재하고 그 OS에서 하이퍼바이저가 구동되며 그 하이퍼바이저 위에서 가상 머신이 실행됩니다.

- 가상머신의 종류로는 VMware, QEMU, 버추얼 PC,서버(MS사 개발, 사용 거의 x), 오라클의 버추얼 박스가 있습니다.

- HW -> 호스트 OS(내 컴퓨터의 윈도우) -> 하이퍼바이저(VMware, VirtualBOX) -> 게스트 OS

- 실습, 개발 테스트 용도

### Type1 vs Type2

- 호스트 OS 유무에 따라 성능 차이가 생긴다. Type1이 더 빠르다.

- 양쪽 모두 VM 하이퍼바이저 자체의 구조는 똑같다. (VMware나 XenServer나 같은 가상 머신 생성 하이퍼바이저다~)

### VirtualBox

- VMware에 비해 구성이 부족하고 버그도 많다.

### NAT

- 복수의 사설 ip를 하나의 공인 ip로 바꿔주는 역할, 반드시 사설 -> 공인은 아니고 NAT의 역할은 특정 ip를 다른 ip로 바꿔주는 역할

### firewalld

- CentOS 기본 방화벽으로 firewalld가 설치되어 실행된다.

- 아파치 WS를 설치해서 방화벽을 허용하는 경우 `firewall-cmd --add-service=http`와 같이 사용할 수 있다.

- 이후 `firewall-cmd --reload`를 해줘야 적용이 완료된다.

### 보안 그룹 vs firewalld

- 퍼블릭 클라우드에서 보안 그룹이 firewall의 대안으로 사용되기 때문에 서버 인스턴스 내에서 방화벽의 설정은 필요하지 않다.

### Host-only 어댑터

- VirtualBox는 기본 NAT 네트워크 어댑터에 포트포워딩이 필요한데 Host-Only 어댑터의 ip를 사용하면 포트 포워딩을 생략할 수 있다.