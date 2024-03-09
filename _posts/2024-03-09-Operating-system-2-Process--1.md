---
title: Operating system (2) Process - 1
subtitle: 1
categories: 2
tags: 3
date: 2024-03-09 12:18:49 +0000
last_modified_at: 2024-03-09 12:18:50 +0000
---


## 프로세스의 개념

> Process is **a program in execution** 
프로세스는 **실행중인 프로그램이다.**
> 

![Untitled](/assets/images/2024-03-09-Operating-system-2-Process--1/Untitled.png)

- 프로세스의 문맥(context)
    - 프로그램의 흐름에서 어떤 특정 지점을 잡았을때 해당 지점까지 어떤 동작이 일어났는지 나타내는 것 (CPU 수행 **상태**를 나타내는 하드웨어 문맥)
    - 프로세스 실행 → 프로세스만의 독자적인 주소 공간 형성(code, data, stack) → CPU를 잡으면 PC가 code 가리킴 → Instruction → CPU 안에서 연산 수행 등
    - 하드웨어 문맥 - CPU는 매 순간 프로세스의 Instruction을 수행함 → PC가 어디까지 수행했는지 code를 가리키면서 알려줌(context), Register에 어떤 값을 넣고 있는지 여부
        - Program Counter
        - 각종 Register
    - 프로세스의 주소 공간
        - code, data, stack
    - 프로세스 관련 커널 자료 구조 - 운영체제는 프로세스들을 관리하는데 프로세스가 하나 생길때마다 그 프로세스를 관리하기 위해 PCB를 data 영역에 둠, A의 커널 스택을 stack 영역에 둠
        - PCB (Process Control Block)
        - Kernel stack - 커널에서 함수 호출이 이루어질 때(ex. 어떠한 프로세스가 System call을 함) 어떠한 프로세스가 요청했는지에 대한 정보를 stack 영역에 해당 프로세스의 커널 스택을 쌓음

![Untitled](/assets/images/2024-03-09-Operating-system-2-Process--1/Untitled%201.png)

![Untitled](/assets/images/2024-03-09-Operating-system-2-Process--1/Untitled%202.png)

- 프로세스의 상태 (Process State) - 프로세스는 상태(state)가 변경되며 수행된다
    - Running
        - CPU를 잡고 instruction을 수행중인 상태
    - Ready
        - CPU를 기다리는 상태(I/O요청이나 다른 물리적인 요청들이 완료 되있어야함. CPU가 바로 수행할 수 있도록!!)
    - Blocked (wait, sleep)
        - CPU를 주어도 당장 instruction을 수행할 수 없는 상태
        - Process 자신이 요청한 event(ex. I/O)가 즉시 만족되지 않아 이를 기다리는 상태
        - 디스크에서 file을 읽어와야 하는 경우
    - Suspended (stopped)
        - 중기 스케줄러에 의해 메모리를 빼앗겨 (외부적인 이유로) 프로세스의 수행이 정지된 상태
        - 메모리 → 디스크 (swap out)
        - ex) 사용자가 프로그램을 일시 정지시킨 경우 (break key)
        시스템이 여러 이유로 프로세스를 잠시 중단시킴
        (메모리에 너무 많은 프로세스가 올라와 있을 때)
    - New - 프로세스가 생성중인 상태
    - Terminated - 수행(execution)이 끝난 상태

> Blocked: 자신이 요청한 event가 만족되면 Ready
Suspended: 외부에서 resume해 주어야 Active
> 

![Untitled](/assets/images/2024-03-09-Operating-system-2-Process--1/Untitled%203.png)

- 줄을 세워서 모든 자원들이 최대한 놀지 않게 하는 것이 필요
- **Queue는 운영체제 kernel이 본인의 Data 영역에 자료구조 Queue를 만들어놓고 프로세스의 상태를 바꿔가면서 운영을 함**

![Untitled](/assets/images/2024-03-09-Operating-system-2-Process--1/Untitled%204.png)

- Process Control Block (PCB) - 운영체제(커널)가 각 프로세스를 관리하기 위해 본인의 data영역에 저장하는 프로세스의 정보
- 구성요소
    - (1) OS가 관리상 사용하는 정보
        - pointer : 초록볼 대기 큐에서 봤듯이 프로세스를 큐에서 관리하기 위해 포인터를 사용
        - Process state - Running, blocked, Ready 등등
        - Process ID - 고유한 숫자를 둬서 관리, 프로세스를 구별하기 위한 숫
        - scheduling information, priority - 먼저 온 순서대로 처리하는게 아니라(라운드로빈x) 우선순위가 높은 순서대로 다음번의 CPU를 넘겨줌
    - (2) CPU 수행 관련 하드웨어 값
        - Program counter, registers - cpu에 어떤 레지스터 값을 넣어 수행하고 있었는가
    - (3) 메모리 관련
        - code, data, stack의 위치 정보
    - (4) 파일 관련
        - Open file descriptors
        - 어떤 파일을 열어놓았는지
        
        ![Untitled](/assets/images/2024-03-09-Operating-system-2-Process--1/Untitled%205.png)
        
- 문맥 교환 (Context Switch) - CPU를 한 프로세스에서 다른 프로세스로 넘겨주는 과정 → 문맥 교환 시에 어느 문맥까지 진행되었는지 여부를 저장하는 과정이 필요함
- CPU를 내어주는 프로세스의 상태를 운영체제의 data에 있는 해당 프로세스의 PCB에 저장하고 넘겨줌
- 다시 프로세스가 CPU를 받아 수행될 때, PCB에 저장되있던 상태부터 수행

![Untitled](/assets/images/2024-03-09-Operating-system-2-Process--1/Untitled%206.png)

### 문맥 교환시 헷갈리는 내용

- 문맥 교환은 CPU 제어권이 프로세스 → 프로세스로 넘어가는 것을 뜻한다. System call이나 Interrupt가 발생해서 사용자 프로세스 → 운영체제로 넘어갔을 때는 문맥교환이라고 부르지 않는다. (사용자 프로세스 A → 운영체제 → 사용자 프로세스 B 일 경우는 문맥교환이라고 침)

![Untitled](/assets/images/2024-03-09-Operating-system-2-Process--1/Untitled%207.png)

![Untitled](/assets/images/2024-03-09-Operating-system-2-Process--1/Untitled%208.png)

위에 첫줄이 Ready Queue이고 그 밑으로 Device Queue들이 있다

PCB의 pointer 영역이 큐 안에서 다음 프로세스를 가리킨다

![Untitled](/assets/images/2024-03-09-Operating-system-2-Process--1/Untitled%209.png)

- 스케줄러 (Scheduler)
    - Short-term scheduler (단기 스케줄러 or CPU scheduler)
        - 어떤 프로세스를 다음번에 running 시킬지 결정
        - 프로세스에 CPU를 주는 문제
        - 충분히 빨라야 함 (millisecond 단위)
    - Long-term scheduler (장기 스케줄러 or job scheduler)
        - admitted - 어떤 new상태의 프로세스한테 메모리를 줄지 안줄지(ready queue) 결정하는 것 (프로세스에 memory 및 각종 자원을 주는 문제)
        - degree of Multiprogramming을 제어 - 메모리에 프로그램이 몇개 올라가있는지를 나타내는 것
        - 보통 우리가 사용하는 time sharing system에는 보통 장기 스케줄러가 없음 (무조건 ready)
    - Medium-Term scheduler (중기 스케줄러 or Swapper)
        - 여유 공간 마련을 위해 프로세스를 통째로 메모리에서 디스크로 쫓아냄
        - 프로세스에게서 memory를 뺏는 문제
        - degree of Multiprogramming을 제어
        - 일단 다 메모리에 올려놓고 너무 많으면 쫓아내는 방식이 현재 시스템 방식
        

---

참조

[운영체제 - 이화여자대학교 | KOCW 공개 강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323)