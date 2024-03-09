---
title: Operating system - System Structure - 1
excerpt: "System Structure - 1"

categories:
  - OS

permalink: /os/1/

toc: true
toc_sticky: true
 
date: 2024-03-09
last_modified_at: 2024-03-09
---
# Operating system-System Structure-1

![Untitled](/assets/images/2024-03-09-Operating-system-System-Structure-1/Untitled.png)

## 컴퓨터 시스템 구조

- CPU : 매 클럭 사이클마다 메모리에서 기계어를 읽어서 실행하게 됨(Instruction = 명령)
    - Register : Memory보다 더 빠르면서 정보를 저장할수 있는 공간
    - Mode bit : CPU에서 실행되고있는것이 운영체제인지 사용자 프로그램인지 구분
        - 0 (커널 모드, 모니터 모드, 시스템 모드) : 운영체제가 CPU에서 수행됨(메모리 접근, I/O 수행)
        - 1 (사용자 모드) : 제한된 Instruction만 CPU에서  수행
        - Interrupt나 Exception 발생시 하드웨어가 mode bit을 0으로 바꿈
        - 사용자 프로그램에게 CPU를 넘기기 전에 mode bit을 1로 세팅
    - Interrupt line : I/O Device 에서 오는 명령을 처리(CPU는 Memory에 있는 작업만을 처리하기 때문에 따로 I/O Device에 접근해 처리하는것이 필요)
    - Timer : Timer에 세팅된 시간만큼 CPU가 Instruction을 실행하도록 설정(Time sharing) → 정해진 시간이 되면 CPU에 Interrupt를 걸
- Memory : CPU의 작업 공간
- I/O Device(키보드, 프린터, 마우스, 하드디스크) : 속도면에서 CPU에 비해 느림
    - Device Controller : I/O Device에 붙어있는 작은 CPU 역할. Device의 역할 통제, 전담, Local Buffer에 데이터 저
        - 프로그램이 I/O 요청 → Device Controller는 해당 Device에서 요청 처리(Control register) → Buffer에 저장 → Controller가 CPU에 Interrupt를 걸어 알려줌 → CPU 제어권 운영체제로 넘어감 → 운영체제는 Buffer에 있는 내용을 프로그램에 Copy → CPU를 사용하고있었던 다른 프로그램이 있다면 할당시간이 끝날때까지 기다린 후, I/O 작업이 필요했던 해당 프로그램 실
    - Local Buffer : Memory같은 역할, 저장공간
- Direct Memory Access Controller (DMA Controller) : CPU만 접근할 수 있는 메모리를 접근할 수 있는 컨트롤러 : Local Buffer에 있는 내용이 작업이 끝나면 해당 컨트롤러가 CPU대신 메모리에 copy함 (Interrupt 과정이 줄어듦. controller와 cpu 간의 interrupt가 한번만 존재하게 )

### 시스템 콜(System call)

- 프로그램이 운영체제한테 요청하는것(I/O 요청)
- 프로그램이 CPU에게 interrupt를 걸어서 mode bit을 바꾸고 CPU가 운영체제에 접근해 I/O요청을 할 수 있도록

### 인터럽트 (Interrupt)

- 인터럽트 당한 시점의 레지스터와 program counter를 save 한 후 CPU의 제어를 인터럽트 처리 루틴에 넘긴다
- Interrupt (하드웨어 인터럽트, 넓은의미) : 하드웨어가 발생시킨 인터럽트
- Trap (소프트웨어 인터럽트)
    - Exception: 프로그램이 오류를 범한 경우
    - System call: 프로그램이 커널 함수를 호출하는 경우
- 인터럽트 처리 루틴 : 해당 인터럽트(시스템 콜, 타이머 인터럽트, 키보드 인터럽트 등 종류)
- 인터럽트 벡터 : 각각의 인터럽트 처리 루틴에 대한 주소

## 동기식 입출력과 비동기식 입출력

![Untitled](/assets/images/2024-03-09-Operating-system-System-Structure-1/Untitled%201.png)

- 동기식 입출력 : I/O 요청 후 입출력 작업이 완료된 후에야 제어가 사용자 프로그램에 넘어감
- I/O가 끝나는 시점은 Interrupt를 보내는 시점
- 구현 방법 1 - I/O가 끝날때까지 CPU 제어권을 넘겨주지 않는것
    - I/O가 끝날 때까지 CPU를 낭비시킴
    - 매시점 하나의 I/O만 일어날 수 있음
- 구현 방법 2 - I/O 요청을 하고 다른 프로그램에 CPU 제어권을 넘겨주는 것
    - I/O가 완료될 때까지 해당 프로그램에게서 CPU를 빼앗음
    - I/O 처리를 기다리는 줄에 그 프로그램을 줄 세움
    - 다른 프로그램에게 CPU를 줌
- 비동기식 입출력
    - I/O가 시작된 후 입출력 작업이 끝나기를 기다리지 않고 제어가 사용자 프로그램에 즉시 넘어감 (I/O 작업을 기다리지 않고 다음 Instruction을 수행함)

## DMA (Direct Memory Access)

![Untitled](/assets/images/2024-03-09-Operating-system-System-Structure-1/Untitled%202.png)

- 빠른 입출력 장치를 메모리에 가까운 속도로 처리하기 위해 사용
- CPU의 중재 없이 device controller가 device의 buffer storage의 내용을 메모리에 block 단위로 직접 전송
- 바이트 단위가 아니라 block 단위로 인터럽트를 발생시킴

![Untitled](/assets/images/2024-03-09-Operating-system-System-Structure-1/Untitled%203.png)

![Untitled](/assets/images/2024-03-09-Operating-system-System-Structure-1/Untitled%204.png)

- Primary - cpu가 직접 접근 가능 (Byte단위)
- Secondary - cpu가 직접 접근 불가

## 프로그램의 실행 (메모리 load)

![Untitled](/assets/images/2024-03-09-Operating-system-System-Structure-1/Untitled%205.png)

![Untitled](/assets/images/2024-03-09-Operating-system-System-Structure-1/Untitled%206.png)

- 프로세스가 차지하고 있는 메모리 주소 공간
    - stack
        - 함수 호출과 관계되는 지역변수(인자), 매개변수가 저장되는 영역, 함수 구조
    - data
        - 변수, 전역변수 등 프로그램이 실행할 자료구조를 담고있음
    - code
        - 프로그램을 실행할 기계어 코드를 담고 있음

## 커널 주소 공간의 내용

![Untitled](/assets/images/2024-03-09-Operating-system-System-Structure-1/Untitled%207.png)

## 사용자 프로그램이 사용하는 함수

![Untitled](/assets/images/2024-03-09-Operating-system-System-Structure-1/Untitled%208.png)

![Untitled](/assets/images/2024-03-09-Operating-system-System-Structure-1/Untitled%209.png)

---

참조

[운영체제 - 이화여자대학교 | KOCW 공개 강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323)