# Table of Contents
- [Flow Control](#flow-control)
- [Ethernet](#ethernet)
- [L1, L2 Layer](#l1-l2-layer)
- [UDP Header](#udp-header)
- [TIME_WAIT](#time_wait)
- [MSL](#msl-maximum-segment-lifetime)

# Flow Control
- 송신자가 수신자의 데이터 처리 속도보다 빠르게 송신하지 못하도록 제어
- 수신 측에서 data overflow를 송신 측에 통보하는 피드백을 전송하는 메커니즘
- Pacing(속도 조절): 송신과 수신의 속도를 일치시키는 것
## 3가지 방식
### 송신제어 방식 (Stop and Wait)
- 프레임을 한 번에 한 개씩 수신 확인을 하는 방식
    - 데이터가 프레임의 최대 크기보다 큰 경우 비효율적

### Rate-based 방식
### Window-based 방식

# Ethernet
- LAN/MAN/WAN에서 가장 많이 활용되는 네트워크 기술 규격
- Physical Layer에선 신호와 배선, Data-link Layer에선 패킷과 프로토콜의 형식을 정의
- 대부분 IEEE 802.3 규약으로 표준화되었음
- Internet과는 다른 개념
    - TCP/IP로 데이터를 주고 받는 컴퓨터 네트워크
    - '73년에 구현된 `네트워크의 네트워크`
- Ether(에테르, 빛의 매질)에서 유래

# L1, L2 Layer
## L1 Layer
- 0과 1 이진수로 이루어진 데이터를 송/수신하는 계층
- 가장 처리 속도가 빠름
## L2 Layer
- 이더넷 프레임을 통해 에러 체킹, 흐름 제어, 접근 제어를 담당
    - 이더넷 프레임은 최대/최소 크기가 모두 같음
    - 다만, 내부 형식(포맷)은 각기 다를 수 있음
        - IEEE 802.3 (RFC 1042)
        - IEEE 802.3 with SNAP Header
        - Ethernet Version 2 (DIX 2.0) (RFC 894)
        - ...

# UDP Header
- TCP의 헤더보다 훨씬 단순하고 크기가 작음
    - 고정된 8바이트의 크기
    - TCP는 20바이트
    - 헤더 처리에 (비교적) 많은 연산이 필요치 않음
## 헤더의 구조 (각 2바이트씩)
1. 발신 포트번호
1. 수신 포트번호
1. 패킷 전체 길이
1. 패킷 전체 체크섬 (선택사항)

# TIME_WAIT
- TIME_WAIT 상태가 존재해야 하는 두 가지 이유
## TCP 신뢰성 보장
- 서버가 보낸 FIN에 대한 ACK가 도착하지 않거나 지정된 시간보다 지연될 경우
    - 서버는 새로운 FIN을 보냄
    - 이 때, 클라이언트의 소켓이 모두 제거되고 CLOSED로 바뀌면 새로운 FIN을 보낼 수 없게 됨
    - 데이터를 송신하면 반드시 수신되어야 하는 TCP의 데이터 무결성(Integrity)이 깨짐

## 새로운 연결
- TIME_WAIT 없이 서버가 CLOSED 상태로 바뀌었다고 가정
- 이 때, 곧바로 새로운 CONNECTION이 동일한 포트 번호로 이루어질 경우
    - 이 패킷이 새로운 연결에서 온 패킷인지 이전 연결에서 지연되어 도착한 패킷인지 구별할 수 없음
- 따라서 TIME_WAIT을 일종의 포트 번호 예약의 개념으로 사용하
    - 새로운 연결은 다른 포트 번호로 할당하여 두 패킷을 구분

# MSL: Maximum Segment Lifetime
- TCP 세그먼트가 인터넷 네트워크 시스템에 존재할 수 있는 최대 시간
