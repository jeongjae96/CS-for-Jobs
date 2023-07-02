# OS
- OS는 컴퓨터 하드웨어와 소프트웨어 리소스를 관리하는 시스템 소프트웨어
- 또한, 컴퓨터 프로그램을 위한 공통의 service(daemon)들을 제공

# Daemon
- 멀티태스킹 OS에서 데몬은 백그라운드에서 실행되는 프로세스이자, interactiva user의 직접적인 제어를 받지 않는 컴퓨터 프로그램

## 일반(전통)적으로 데몬의 프로세스명은 알파벳 `d`로 끝남
    - 프로세스가 데몬임을 명확하게 하고
    - 일반 컴퓨터 프로그램과 다름을 나타내기 위함
    - 예시
        - `syslogd`: 시스템 로깅을 implement한 데몬
        - `sshd`: Incoming SSH 연결을 담당하는 데몬

# Computer Program
- A sequence or set of insturctions in a programming language for a computer to execute
- One component of software, which also includes documentation and other intantible components

# Firmware
- Specific class of computer software
- Provides low-level control for device's specific hardware

## Such as the `BIOS` of a PC, may contain/provide
- Basic functions of a device
- Hardware abstraction services to higher-level software such as `OS`
    
## For less complex devices, firmware act as the device's complete `OS`
- Perform all control, monitor, data manipulation functions

## Typical examples of devices containing firmware are 
- `Embedded Systems(Running Embedded Software)`
- Home and personal use appliances
- Computers
- Computer Peripherals
    - An auxilary hardware device used to transfer information into and out of a computer
    - I/O/Both devices

# OS의 역할
## CPU Secheduling and Process Managing
- CPU 자원 소유권을 어떤 프로세스에 할당할지
- 프로세스의 생성/삭제 & 자원 할당/반환을 관리

## Memory Managing
- 컴퓨터 메모리 자원은 한정되어 있음
- 이 메모리 자원을 어떤 프로세스에 얼만큼 할당해야 하는지 관리

## Disk File Managing
- 디스크 파일을 **어떤 방법**으로 보관할지 관리

## I/O Device Managing
- I/O 디바이스와 컴퓨터간 데이터를 주고 받는 것을 관리

# OS의 구조
1. GUI/CLI(CUI)
- 일부 리눅스 서버용 OS는 CLI만 구현되어 있는 경우도 있음
1. System Call
1. Kernel
1. Driver(Device Driver)

## System Call
- **Kerenl에 접근**하기 위한 인터페이스
- User Application이 OS의 서비스를 제공받기 위해 **`커널 함수`를 호출할 때 사용**
- 작동 예시
    1. User App이 I/O 요청으로 트랩(trap)을 발동
    1. 유효한 I/O 요청인지 확인
    1. System Call을 통해 유저 모드에서 커널 모드로 변환
    1. 커널 모드에서 로직 수행
    1. 다시 유저 모드로 돌아와 후행 로직 수행
- System Call은 추상화된 계층
    - 프로그램을 구현할 때 I/O 작업뿐만 아니라 네트워크 통신 등 low-level에 대한 코드를 매번 구현하는 수고를 덜 수 있는 장점

# modebit
- 유저 모드와 커널 모드를 구분하기 위한 플래그 변수
- 값이 0이면 커널, 1이면 유저 모드
- 작동 예시
    1. User Application이 디바이스의 카메라를 사용하려 함
    1. 시스템 콜 호출
    1. **`modebit`을 1에서 0으로**
    1. 커널 모드에서 카메라 사용을 포함한 로직 수행
    1. **`modebit`을 0에서 1으로**
    1. 다시 유저 모드로 돌아와 후행 로직 수행

# Kernel
- Is a **computer program** that core of a computer's `OS`
- Generally has complete control over everything in the system
- 시스템콜 인터페이스를 제공

# 컴퓨터의 요소
- CPU
- DMA 컨트롤러
- 메모리/컨트롤러
- 타이머
- 디바이스 컨트롤러 
- 로컬 버퍼 등...

# CPU
- 명령어를 해석하여 실행하는 장치
- ALU, CU, Register 등으로 구성되어 있음
- **인터럽트**에 의해 메모리에 로드된 명령어를 해석

## Control Unit
- 프로세스 조작(control(?))을 담당
- I/O 통신을 제어
- 명령어를 읽고 해석
- 데이터 처리를 위한 순서를 결정

## Register
- 프로세서에 빠르게 접근 가능한 기억장치
- 1차 메모리보다 빠른 속도로 데이터를 적재/저장할 수 있음

# Interrupt
- 현재 처리 중이던 기계어의 처리를 잠깐 멈추고 다른 로직으로 점프하여 해당 로직을 수행하는 것
- 종류
    - I/O Device
    - Division by Zero 
    - 프로세스 오류 등...
- 작동 순서
    1. 인터럽트 발생
    1. 인터럽트 벡터 내의 인터럽트 핸들러 함수 호출 및 실행
- 인터럽트 핸들러 함수는 커널 내부의 Interrupt ReQuest를 통해 호출됨
    - `request_irq()`를 통해 인터럽트 핸들러 함수를 등록할 수 있음
- 인터럽트간 우선순위가 존재
- H/W, S/W 인터럽트로 나누어짐

# DMA Controller
- TBC...
