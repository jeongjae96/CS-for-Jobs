# Process
- 하나 혹은 그 이상의 스레드에서 **실행되는** 컴퓨터 프로그램
- CPU 스케줄링의 대상이 되는 `task`와 유사어

# CPython
- 파이썬 프로그래밍 언어의 Reference Implemantation
- 인터프리터와 컴파일러 둘 다로 정의할 수 있음
    - 파이썬 코드를 `Bytecode`로 **컴파일**
    - `Bytecode`를 **인터프리팅**
- C뿐만 아니라, 파이썬으로도 작성됨
- Default이면서 동시에 가장 널리 사용되는 Python Language의 implementation

# Preprocessing
- Input data를 processing 하여 다른 프로그램의 input data로 변환하는 일
- 본 예시에서는 input data가 C언어 source code가 되고, C 컴파일러가 다른 프로그램이 된다
- 전처리된 파일의 확장자는 i임
    - filename.i
    - i인 별 뜻은 없고, intermediate form이어서 그렇다는 챗지피티의 답변
        - After preprocessing, Before compilation...

# Process Control Block
- Process Descriptor와 동의어
- OS에서 사용하는 프로세스에 관련한 모든 정보(메타데이터)를 저장하기 위한 자료구조
- 프로세스 상태를 명시하거나 트래킹하기 위한 용도로도 사용
- PCB가 트래킹 용도로 사용되면, PCB는 컨택스트 스위칭에서 중요한 역할이 됨
    - PCB 저장과 로드를 토대로 컨텍스트 스위칭을 수행하기 때문
- 프로세스의 주솟값들이 스택/힙 등의 구조로 할당되며, 이 주솟값들을 PCB로 관리
- 프로세스의 중요한 정보를 담고 있기 때문에 User가 접근하지 못하도록 *커널 스택의 가장 앞부분*에서 관리됨
    - 커널 스택?

## Contents of PCB
### Process State(Scheduling State)
- Ready, Running 등의 프로세스 상태

### Process ID
- PID, Child Process's PID

### Process Privileges
- Allowed/Disallowed access to system resources

### Program Counter
- 프로세스는 CPU에 의해 순차적으로 실행되는 instruction을 포함한다
- PC는 현재 프로세스에서 다음 순서에 실행되어야 하는 instruction의 주소값을 담고 있다 -> 포인터!

### CPU Registers
- Running 상태에서 process의 execution을 위해 저장해야 하는 register의 집합

### CPU Scheduling Information
- CPU 스케줄러에 의해 중단된 시간 등에 대한 정보
- Information Scheduling CPU time; Wikipedia

### Accounting Information
- 프로세스 실행에 사용된 CPU 사용량
- 프로스를 실행한 유저의 정보
- Time Limits
- Execution ID 등

### I/O Status Information
- List of I/O devices allocated to the process; Wikipedia

# fork and exec
- Both are `system call` in UNIX-like OS

## fork()
- 부모 프로세스의 주소 공간을 복사하여 자식 프로세스를 생성하는 함수
- 주소 공간의 복사는 이루어지지만, 비동기 작업 등을 상속하지는 않음
- 부모/자식은 parent/child가 번역된 것임

## exec()
- 새로운 프로세스를 생성하는 함수

# Process status
## Create
- fork/exec 함수를 통해 프로세스가 생성된 상태
- 이 상태에서 PCB가 할당됨
- Ready 상태로만 변화할 수 있음

## Ready
- CPU 스케줄러로부터 CPU 소유권을 넘겨받기를 기다리는 상황
- 메모리 할당을 반드시 받은 상태는 아님(받지 못한 상태로 대기할 중일 수 있음)
- Ready Suspended 혹은 Running 상태로 변화할 수 있음

## Ready Suspended
- 메모리 부족으로 일시 중단된 상태
- Ready 상태로만 변화할 수 있음

## Running
- CPU 소유권과 메모리를 할당받은 상태에서 인스트럭션을 수행 중인 상태
- CPU Burst가 일어났다고도 표현
- Terminate, Ready, Blocked 상태로 변화할 수 있음(#3)

## Blocked
- 어떤 이벤트가 발생한 이후 프로세스가 차단된 상태
- Event Occuring 등으로 프로세스는 blocked 상태로 전환될 수 있음
    - I/O 디바이스 인터럽트 등
- Blocked Suspended 혹은 Ready 상태로 변화할 수 있음

## Blocked Suspended
- Ready Suspended와 유사
- Blocked 상태에서 프로세스가 재실행되려고 했지만, 메모리 부족으로 일시 중단된 상태
- 다시 Blocked 혹은 Ready Suspended 상태로 변화할 수 있음

## Terminated
- 메모리와 CPU 소유권을 모두 놓고(?) 가는 상태
    - 더이상 점유하지 않는다는 말인 듯
- 자연스러운(?) 종료와 비자발적 종료(Abort)로 나뉨

### Abort
- 부모 프로세스에 의해 자식 프로세스가 강제로 종료되는 일
- 자식 프로세스에 할당된 자원의 한계치를 넘거나
- 부모 프로세스가 종료되거나
- `process.kill` 등 여러 명령어로 프로세스를 종료하는 경우 발생

# Memory Layout
- C로 작성된 프로그램은 다음 구조를 기반으로 메모리를 할당 받음

## Stack
- 아래 변수들이 할당되는 동적영역
    - Local Variable
    - Function
    - Function Parameter
- 동적으로 크기가 결정되므로, 런타임에서 힙 영역을 침범하면 안 되기 때문에 둘 사이의 공간을 비워 놓음
    - Free Space
- 위와 같은 이유로 높은 주소에서 낮은 주소 순서로 할당함
- Stack Pointer Register가 Top of Stack을 트래킹함
- Stack Frame들을 담는 Call Stack을 포함하는 영역
    - Call Stack 외의 정보가 포함되는지는 확실하지 않음

## Heap
- 동적할당되는 변수들이 할당되는 영역
- BSS Segment의 끝에서부터 시작
- 아래 함수들로 관리됨
    - malloc()
    - calloc(): malloc()과 기능은 같으나 모든 원소의 값을 0으로 초기화함
    - realloc(): 할당받은 메모리의 크기를 `size` 매개변수만큼 조정하여 재할당함
        ```C
        void *realloc(
            void    *memblock,
            size_t  size
        );
        ```
    - free(): 할당받은 메모리 해제
- 상대적으로 더 큰 객체를 다루기 때문에, Stack Space보다 Heap Space의 크기가 일반적으로 더 큼

## BSS Segment
- 초기화되지 않은 정적변수 및 상수가 저장되는 영역
    - 초기화되지 않음 == 값이 0으로 시작했거나, 명시되어 있지 않은 것
    - 변수들의 값이 0으로 초기화되어 저장됨

## Data Segment
- 초기화되었고, 값 변경이 가능한 정적변수들이 저장되는 영역
- 초기화된 상수는 ``.rodata` 영역에 저장됨
    - Data Segment에 저장되는지는 확실하지 않음

## Code Segment
- Object 파일의 일부이거나, 
- Executable instruction들을 포함하는 프로그램의 가상 메모리 영역에 *대응되는 영역*
    - Corresponding Section
- 수정 불가능한 기계어들이 저장되는 영역
- Text Segment(simply as text)와 동의어

# Context Switching
- PCB를 교환하는 과정
- 아래 설명은 `싱글코어` 기준임
- 한 프로세스에 할당된 시간이 끝나거나 인터럽트에 의해 발생
- 어떤 시점에서 실행되고 있는 프로세스는 하나
    - 많은 프로세스가 동시에 구동되는 것처럼 보이는 것은 컨텍스트 스위칭이 아주 바르게 실행되기 때문
- PCB를 저장하고 로드하는 과정에서 Idle time이라는 비용이 발생
- 추가적으로 캐시미스라는 비용도 발생

## *Cache Miss*
- 컨텍스트 스위칭이 일어날 때 프로세스가 가지고 있는 메모리 주소가 그대로 있으면 잘못된 주소 변환이 생김
- 따라서 캐시 클리어를 해야 하고 이 때문에 캐시미스가 발생

## Context Switching in Thread
- 스레드에서도 컨텍스트 스위칭이 일어남
- 스레드는 스택영역을 제외한 메모리의 모든 영역을 공유함
    - 따라서 스레드 컨텍스트 스위칭은 상대적으로 저비용(시간이 더 적게 걸림)

# Multiprocessing
- 동시에 두 가지 이상의 프로세스를 실행하는 것
- 하나 이상의 일을 병렬로 처리할 수 있음
- 특정 프로세스의 메모리 혹은 다른 일부에 문제가 발생하더라도, 다른 프로세스를 이용해서 작업을 처리할 수 있음
    - 신뢰성이 높은 장점

## Web Browser's Multiprocessing Structure
- Browser Process
    - 주소 표시줄, 북마크 막대, 뒤로/앞으로 가기 버튼
    - 네트워크 요청, 파일 접근 권한 등을 담당
- Renderer Process
    - 보이는 부분 즉, 화면에 그리는 모든 것을 제어/담당
- Plugin Process
    - 웹 사이트에서 사용하는 플러그인을 제어/담당
- GPU Process
    - GPU를 사용하여 화면을 그리는 부분을 제어/담당

# IPC
- Inter Process Communication
- 프로세스끼리 주고받는 데이터와 공유 데이터를 관리하는 메커니즘
- 아래와 같은 종류가 있음
    - Shared Memory
    - File
    - Socket
    - Unnamed Pipe
    - Named Pipe
    - Message Queue
- 메모리가 완전 공유되는 스레드보다는 속도가 떨어지는 단점

## Shared Memory
- 두 개 이상의 프로세스에 동일한 메모리 블록에 대한 접근 권한을 부여해서, 프로세스들끼리 서로 통신할 수 있도록 공유 버퍼를 생성하는 것
- 임의의 매개체를 통한 데이터 송수신이 아니라 메모리 자체를 공유하는 IPC
    - 불필요한 데이터 복사 오버헤드가 발생하지 않아, IPC 방식 중에서 가장 빠르다
    - 메모리 영역을 공유하는 것이기 때문에 동기화가 필요함
- 하드웨어 관점에서, RAM은 CPU가 접근할 수 있는 큰 랜덤 공유 메모리임

## File
- 디스크에 저장된 데이터 혹은 파일 서버에서 제공한 데이터를 칭함
- 이를 기반으로 프로세스 간 통신이 이루어짐

## Socket
- 동일한 컴퓨터의 다른 프로세스 혹은 네트워크 인터페이스를 통해 다른 컴퓨터와 데이터를 송수신하는 것
- TCP/UDP 소켓이 있음

## Unnamed Pipe
- 프로세스 간에 FIFO 방식으로 읽히는 임시 공간인 Pipe를 기반으로 데이터를 송수신하는 것
- 읽기 혹은 쓰기 전용의 단방향 방식
- 부모-자식 프로세스 간에만 사용 가능
    - 다른 네트워크 상의 기기에는 사용 불가!

## Named Pipe
- 파이프 서버와 하나 이상의 파이프 클라이언트 간의 통신을 위한 명명된 파이프
- 단방향과 이중 파이프 모두 존재
- 파이프는 하나 이상 있을 수 있고 동시에 사용할 수도 있으며, 파이프를 인스턴스로 열어서 사용
    - 파이프가 하나인 경우 서버용 파이프로 구분하는 것 같음(확실하진 않음)
    - 여러개인 경우 클라이언트용 파이프로 구분하는 듯
- 컴퓨터 프로세스뿐만 아니라 다른 네트워크 상의 기기에도 사용 가능!

## Message Queue
- 메시지를 Queue Data Structure로 관리하는 것
- 커널에서 전역변수 등 전역적으로 관리됨
- 다른 IPC 방식에 비해 매우 직관적이고 간단하여 공유 메모리의 동기화를 구현하는 것이 복잡할 때 대안으로써 사용하기도 함
    - 다른 코드의 수정 없이 추가 코드 켳 줄로 간단하게 메시지 큐에 접근할 수 있음
    - 공유 메모리로 IPC를 구현할 때, 데이터 R/W 빈도가 높으면 동기화 기능 구현이 까다로움

### MQ의 장점
- Asynchronous
    - 동기화가 필요 없고, 메시지를 큐에 넣어놓고 나중에 처리할 수 있음
    - 한꺼번에 가용 처리량보다 많은 메시지가 들어올 경우 병목이 발생할 수 있고, 후순위로 들어오는 요청에 대한 응답이 지연될 수 있음
- Decoupling
    - Producer 서비스와 Consumer 서비스가 독립적으로 존재하며 결합도 및 의존도가 낮아짐
- Scable
    - Producer 혹은 Consumer가 원하는 대로 확장할 수 있음
- Resilience
    - Consumer가 다운되더라도 Application이 다운되는 것은 아님
    - 메시지는 큐에 남아 있기 때문에, 다시 Consumer 서비스가 올라오면 추가 설정/작업이 필요 없이 메시지를 처리할 수 있음
- Guarantees
    - 큐에 보관된 메시지가 당장은 아니더라도 결국 Consumer에 전달된다는 것을 보장

# Thread
- 프로세스에서, 실행 가능한 가장 작은 단위
- 하나의 프로세스는 여러 스레드를 가질 수 있음
- 스레드는 Code/Data/BSS Segment, Heap Space를 서로 공유하고 그 외는 각각 생성
    - 프로세스는 Code/Data/BSS Segment, Heap Space뿐만 아니라 Stack Space 등도 각각 생성
    - 스레드끼리도 Stack Space는 공유하지 않음

# Multithreading
- 프로세스 내 작업을 여러 스레드로 처리하는 기법
- 스레드끼리는 서로 공유하는 자원이 많은 점을 효율적으로 이용함
- 새 프로세스를 생성하는 것보다 스레드를 사용(생성?)하는 것이 훨씬 리소스 소모가 적은 장점
- 하나의 스레드가 blocked 되어도 다른 스레드가 running 상태라면, 무중단 고속 처리가 가능
- 동시성(Concurrency)에도 장점으로 작용
    - 서로 독립적인 작업들을 작은 단위로 나누고 동시에 실행되는 것처럼 보여주는 것
- 한 스레드에 문제가 생기면 다른 스레드에도 영향을 주고, 프로세스 전체에 영향을 줄 수도 있는 단점

## Web Browser Renderer Process Example
- Main Thread
- Worker Thread
- Compositor Thread
    - 모던 브라우저는 웹 페이지의 각 부분을 레이어로 나누어서 처리함
    - Commit 된 각 레이어를 래스터화하고, 웹 페이지로 합성하는 스레드
- Raster Thread

# Shared Resource
- 두 개 이상의 프로세스 또는 스레드가 동등하게 접근할 수 있는 자원 혹은 변수
    - 모니터, 프린터, 메모리, 파일, 데이터 자원 등
- 공유 자원을 두 개 이상의 프로세스가 동시에 읽거나 쓰는 상황을 race condition이라고 함
    - 스레드는 아닌가?

# Critical Section
- 두 개 이상의 프로세스 또는 스레드가 공유 자원에 접근할 때, 순서 등의 이유로 작업 수행 결과가 달라지는 영역
- 일반적인 해결법 3가지
    - Mutex
    - Semaphore
    - Monitor
- 세 가지 해결법 모두 아래 조건을 만족하며, lock 매커니즘을 토대로 함
    - Mutual Exclusion
        - 한 프로세스가 임계 영역에 들어갔을 때, 다른 프로세스는 그곳에 들어갈 수 없다
    - Bound Waiting
        - 특정 프로세스가 영원히 임계 영역에 들어가지 못하면 안 된다
    - Progress
        - 어떠한 프로세스도 임계 영역을 사용하지 않는다면, 임계 영역 외부의 어떠한 프로세스도 들어갈 수 있음
        - 이때, 프로세스끼리 서로 방해하지 않음

## Mutex
- 공유 자원을 lock(lock 함수)을 통해 잠그고, 사용 후에는 unlock을 통해 잠금을 해제하는 *객체*
- 잠기면 다른 프로세스 혹은 스레드는 *잠긴 코드 영역*에 접근할 수 없고, 해제는 그 반대
- 잠금 또는 잠금 해제라는 상태만을 가짐

## Semaphore
- 일반화된 뮤텍스
- 간단한 정수 값을 가지는 변수
- 두 가지 atomic function으로 조작되고, 변수 값을 수정하는 연산은 모두 원자성을 만족해야 함(두 개 이상의 프로세스가 동시에 세마포어 값을 변경할 수 없다)
    - wait() == P()
        - Try의 네덜란드어 Proberen
        - 임계 구역에 들어가기 전에 수행
    - signal() == V()
        - Increment의 네덜란드어 Verhogen
        - 임계 구역에서 나올 때 수행
- 구현 예시
    - Busy Waiting을 이용한 최초 제시 방법
    ```
    P(S) {
        while S <= 0;  // Do nothing
        S--;
    }

    V(S) {
        S++;
    }
    ```
    - 위 방법의 단점을 보완한 *재움 큐*를 활용한 방법
    ```
    P(S) {
        S--;
        if s < 0
            // Add this process to 재움 큐(Sleep)
    }

    V(S) {
        S++;
        if S <= 0
            // Remove process from 재움 큐(Wakeup)
    }
    ```
- 모든 deadlock을 해결할 수 있는 것은 아님

## Binary Semaphore
- 세마포어 값으로 0 또는 1만을 가짐
- Counting Semaphore보다 구현이 간단
- Test and Set 등 하드웨어가 지원하는 기능을 이용해 구현하기도 함
- 이를 이용하여 Counting Semaphore를 구현할 수도 있음
- Mutex는 이것과 유사하지만, 신호 매커니즘이 아닌 잠금 매커니즘이기 때문에 동치는 아님

## Counting Semaphore
- 활용 가능한 자원의 갯수로 초깃값을 지정
- 값의 범위는 미리 정해져 있지 않음

## Monitor
- 공유 자원에 대한 접근을 별도로 구현된 인터페이스로만 접근할 수 있도록 하는 방법
- 모니터 큐를 통해 공유 자원에 대한 작업들을 순차적으로 처리
- 세마포어보다 구현이 쉬움
- Mutual Exclusion을 따로 구현할 필요가 없음
    - 세마포어는 명시적으로 구현해야 함

# Deadlock
- 교착 상태
- 두 개 이상의 프로세스들이 서로가 가진 자원을 기다리며 실행이 중단된 상태

## 원인(교착 상태의 조건)
- 아래 조건들 중에서 한 가지라도 만족하지 않으면 데드락은 발생하지 않음
- Circular Wait는 Hold and Wait과 No-preemption을 만족해야 성립되므로, 각 조건들은 서로 완전히 독립적이지 않음
- Mutual Exclusion: 프로세스들이 필요로 하는 자원에 대해 배타적인 통제권을 요구함
- Hold and Wait: 프로세스가 할당된 자원을 가진 상태에서 다른 자원을 기다림
- No-preemption: 프로세스가 어떤 자원의 사용을 끝낼 때까지 그 자원을 뺏을 수 없음
- Circular Wait: 각 프로세스는 순환적으로 다음 프로세스가 요구하는 자원을 가지고 있음

## 예방법
- 대부분의 현대 OS들은 데드락을 100% 막는 것은 불가능
- 데드락이 발생하면 OS들은 각기 다른 비표준 방식으로 대응
- 대부분의 접근은 위 4가지 조건(코프먼 조건)들 중 하나를 막음으로써 동작
    - 일반적으로 Circular Wait 조건

### Mutual Exclusion 제거
- 상호 배제 조건 자체를 제거(공유 불가능을 제거)

### Hold and Wait 제거
- 한 프로세스가 수행되기 전에 모든 자원을 할당하고, 점유하지 않을 때에는 다른 프로세스가 자원을 요구하도록 하는 방법
- 자원 과다 사용으로 인한 효율성, 요구 자원을 파악하는 데 드는 비용, 자원에 대한 내용을 저장/복원하기 위한 비용, starvation, 무한 대기 등의 문제가 있음

### No-preemption 제거
- 비선점 프로세스에 대해 선점 가능한 프로토콜을 만들어 줌

### Circular Wait 제거
- 자원의 유형에 따라 순서를 매김

## 해결법
- Banker's Algorithm
- 데드락이 발생하면 사이클이 있는지 찾아보고, 이와 관련한 프로세스를 한 개씩 지움
