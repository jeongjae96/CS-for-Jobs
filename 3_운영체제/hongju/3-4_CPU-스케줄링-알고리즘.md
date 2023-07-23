# CPU 스케줄러
- CPU 스케줄러는 CPU가 처리해야 하는 특정 프로세스의 연산을 CPU에 `스레드` 단위로 할당함
- CPU 스케줄링 알고리즘에 의해 작동
- 아래 역할을 수행하는 것이 존재 목적
    - CPU 이용률 증대
    - 주어진 시간 내 많은 일을 수행
    - Ready Queue에 적은 프로세스가 존재하도록 함
    - 응답 시간을 짧게 단축

# CPU 스케줄링 알고리즘
- 선점형(Preemptive), 비선점형(Non-preemptive)으로 나뉨

## 선점형(Preemptive)
- RR
- SRF (Shortest Reamin-time First, Preemptive variant of SJF)
- 다단계 큐

## 비선점형(Non-preemptive)
- FCFS
- SJF
- 우선순위

# Convoy Effect
- Long processes can be holding the CPU, causing the short processes to wait for a long time.
    - Wikipedia

# Non-preemptive Type
- 프로세스가 스스로 CPU 소유권을 포기하는 방식
- 따라서 OS가 강제로 프로세스를 중지하지 않음
- 컨텍스트 스위칭으로 인한 부하가 적은 것이 장점

## FCFS
- First Come First Served
- 가장 먼저 온 프로세스부터 먼저 처리하는 알고리즘
- Long process 때문에 ready queue에서 오래 기다리는 convoy effect가 발생할 수 있는 단점

## SJF(SJN)
- Shortest Job First(Next)
- 실행시간이 가장 짧은 프로세스부터 먼저 처리(실행)하는 알고리즘
- 평균 대기시간이 가장 짧은 것이 장점
- 긴 실행시간을 가진 프로세스가 실행되지 않는 현상(Starvation)이 발생할 수 있는 단점
- 프로세스가 과거에 실행됐던 시간을 토대로 실행시간을 예측
    - 예측 방법: TBC
- Multilevel Feedback Queue?

## 우선순위
- SJF의 Starvation 문제를 보완하고, Aging 개념이 들어간 알고리즘을 말함

# Preemptive Type
- 현대 OS가 쓰는 방식
- 현재 CPU를 사용하고 있는 프로세스를 중단시키고, 강제로 CPU 소유권을 회수해 다른 프로세스에 할당하는 방식

## Round Robin Scheduling
- Priority Scheduling의 일종
- Time Quantum까지만 실행하다가, 작업이 남으면 Ready Queue의 끝으로 이동시키는 방식
    - TQ는 보통 10-100 milliseconds
    - TQ가 너무 크면 FCFS가 됨
    - TQ가 Context Switch Time보다 작으면 오버헤드가 심화됨
- 어떤 프로세스가 대기하는 시간: (N_PROCESS - 1) * TIME_QUANTUM
- 전체 작업 시간은 길어지는 단점, 평균 응답 시간은 짧아지는 장점
- 로드 밸런서에서 트래픽 분산 알고리즘으로도 사용됨

## SRF
- Preemptive Variant of SJF
- 어떤 프로세스를 실행하는 도중 실행 시간이 더 짧은 프로세스가 들어오면, 현재 실행을 중지하고 해당 프로세스를 실행하는 알고리즘

## 다단계 큐
- 우선순위에 따른 Ready Queue를 여러개 사용
- 큐마다 RR이나 FCFS등 다른 스케줄링 알고리즘을 적용한 것
- 큐 간 프로세스 이동이 불가능하여 스케줄링 부담은 적은 장점, 유연성이 떨어지는 단점
- 예시
    - 높은 우선순위\[FCFS\]: 시스템 프로세스
    - 중간 우선순위\[SJF\]: 상호작용을 하는 프로세스
    - 낮은 우선순위\[RR\]: 배치 프로세스
