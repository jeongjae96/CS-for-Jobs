# HTTP
- Application Layer에서 사용하는 웹 서비스 통신에 사용하는 프로토콜
- HTTP/1.0부터 시작해서 HTTP/3 버전까지 있음

# HTTP/1.0
- 한 연결당 하나의 요청만을 처리
    - 단일 파일 하나를 가져올 때마다 TCP Handshake 열어야 함
        - 총 Round Trip Time 갯수 증가

# RTT 갯수 증가 해결 방법
## Image Spliting
- 필요한 모든 이미지들이 담긴 단일 이미지 파일 하나를 다운로드하고, CSS 등을 사용하여 필요한 각 이미지를 분할하여 사용하는 방법
    - background-position 등

## Code Compression
- 개행문자, 공백문자 등을 제거하여 소스파일 용량을 줄이는 방식

## Image Base64 Encoding
- 이미지를 문자열로 인코딩하여 애초에 이미지 파일을 다운로드할 필요가 없게 하는 방식
- 트레이드 오프로 원본 이미지보다 더 많은 용량이 필요할 수 있음: 그 크기 및 검증 필요

# HTTP/1.1
- 매번 TCP 연결을 여는 방식이 아니고, `keep-alive` 옵션을 사용하는 방식
    - `keep-alive`는 HTTP/1.0에도 있었으나, 표준 및 기본 옵션이 아니었음
    - 여러번의 `GET-RESPONSE`를 한 번의 연결로 처리할 수 있음
    - 요청하는 리소스의 갯수에 비례해서 `대기시간`이 길어지는 방식이라 마스터 키는 아님

## Cons
### HOL Blocking
- 네트워크 큐에 있는 첫 번째 패킷이 처리가 지연되면 후방 패킷까지 같이 지연되는 문제
- 단일 요소에 의한 전체적인 성능 저하, 병목현상

### Heavy Header
- 쿠키를 비롯한 많은 양의 메타데이터가 헤더에 들어가 있고, 압축이 되지 않아 무거움

# HTTP/2
- 구글이 개발한 비표준 개방형 네트워크 프로토콜에서 파생
- HTTP/1.X 보다 지연 시간은 줄고, 응답 시간은 빠른 장점
- 멀티플렉싱, 헤더 압축, 서버 푸시, 요청 우선처리 등을 지원

## Multiplexing
- 하나의 연결 내에서, 데이터 송수신에 다수의 병렬적인 스트림을 사용하는 방식
- 특정 스트림의 패킷이 손실되더라도, 해당 스트림 외의 스트림에는 영향을 끼치지 않는 장점
- `HOL Blocking` 문제 해결

## Header Compression
- Huffman Coding을 사용하는 HPACK 압축을 사용

### Huffman Coding
- 문자열을 문자 단위로 쪼개고, 빈도가 높을 수록 적은 양의 비트를 사용하는 방식

## Server Push
- HTTP/1.1에서는 클라이언트가 CSS, 이미지 등의 파일을 요청해야만 받을 수 있었음
- HTTP/2부터는 클라이언트의 요청이 없어도 서버가 클라이언트에 파일을 푸시할 수 있음

# HTTPS
- SSL/TLS 계층이 있어 신뢰할 수 있는 HTTP 요청을 말함
    - SSL/TLS 계층은 Application Layer와 Transport Layer 사이에 위치
    - SSL/TLS 계층을 통해 **통신을** 암호화
- HTTP/2는 HTTPS 위에서 동작함

## HTTPS 구축 방법
- CA에서 구매한 인증키를 기반으로 HTTPS 서비스를 구축
- 서버 앞단에 HTTPS를 제공하는 로드 밸런서를 배치
- 서버 앞단에 HTTPSfmf 제공하는 CDN을 배치

## Secure Socket Layer/Transport Layer Security Protocol
- SSL 1.0 ~ ... ~ TLS 1.0 ~ ... TLS 1.3의 버전
- 제 3자(인터셉터)가 메시지를 도청하거나 변조하지 못하도록 하는 역할
- 보안 세션을 기반으로 데이터를 암호화

## 보안 세션
- 보안이 시작되고 끝나는 동안 유지되는 세션
- SSL/TLS는 핸드셰이크를 통해 보안 세션을 생성하고 이를 기반으로 상태 정보 등을 공유함

# HTTP/3
- 웹에서 정보를 교환하는데 사용되는 HTTP의 세 번째 버전(세대)
- TCP 계층 위에서 돌아가는 HTTP/2와 달리, QUIC 계층 위에서 돌아감
    - 그리고 TCP가 아닌 UDP 기반임

## 초기 연결 설정 시 latency 감소
- TCP 기반이 아니기 때문에 TCP Handshake가 필요 없음
- 클라이언트가 서버에 어떤 신호를 한 번 주고, 서버가 거기에 응답하기만 하면 바로 본 통신을 시작
    - 첫 연결 설정에 1 RTT만 소요됨
- Forward Error Correction이 적용됨

## Forward Error Correction
- 순방향 오류 수정
- 전송한 패킷이 손실되었다면, 수신하는 측에서 에러를 검출하고 수정하는 개념
- 원활하지 않은 네트워크 환경에서도 낮은 패킷 손실률을 기대할 수 있음
