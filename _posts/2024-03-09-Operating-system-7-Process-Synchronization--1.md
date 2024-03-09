---
title: Operating system (7) Process Synchronization - 1
subtitle: 1
categories: 2
tags: 3
date: 2024-03-09 12:36:44 +0000
last_modified_at: 2024-03-09 12:36:45 +0000
---


![Untitled](/assets/images/2024-03-09-Operating-system-7-Process-Synchronization--1/Untitled.png)

### Race Condition

- S-box를 공유하는 E-box가 여럿 있는 경우 Race Condition(경쟁 상태)의 가능성이 있음
    - S-box : Memory Address Space
    - E-box : CPU Process
- 프로세스들간의 공유메모리를 사용하면 문제가 생길수 있음(멀티 쓰레드도 연관, 커널 데이터)

> **공유 메모리(shared memory)** 방식에서는 프로세스들이 주소 공간의 일부를 공유한다. 이 공유한 메모리 영역에서 read/write하면서 통신을 수행한다.
> 

![Untitled](/assets/images/2024-03-09-Operating-system-7-Process-Synchronization--1/Untitled%201.png)

### OS에서의 race condition

1. kernel 수행 중 인터럽트 발생 시
2. Process가 system call을 하여 kernal mode로 수행 중인데 context switch가 일어나는 경우
3. Multiprocessor에서 shared memory 내의 kernel data

![Untitled](/assets/images/2024-03-09-Operating-system-7-Process-Synchronization--1/Untitled%202.png)

고급 언어로 된 Count++라는 코드를 수행할 때, Count 변수를 메모리의 data 영역에서 불러와서, CPU 안의 Register에 넣고, Register 값을 증가시키고(++), 다시 메모리의 변수 위치에 갖다 넣는 여러개의 Instruction으로 수행됨.

### 해결 방법

주요한 변수 값을 건드리는 동안(Instruction 수행중)에는 Interrupt가 들어왔을때 CPU를 넘기는게 아니라 해당 작업이 끝날때까지 Interrupt 처리를 하지 않아야 함.

![Untitled](/assets/images/2024-03-09-Operating-system-7-Process-Synchronization--1/Untitled%203.png)

![Untitled](/assets/images/2024-03-09-Operating-system-7-Process-Synchronization--1/Untitled%204.png)

할당 시간 초과시 커널 모드 작업중이었어도 다른 프로세스가 선점하게 되는 문제가 있음

### 해결 방법

프로세스가 커널 모드에 있을때는 할당 시간이 끝나도 CPU를 뺏기지 않도록 해야함

![Untitled](/assets/images/2024-03-09-Operating-system-7-Process-Synchronization--1/Untitled%205.png)

CPU가 여러개 있는 환경에서는 위에 있는 방법이 해결책이 되지 않음.

CPU가 어떤 공유 데이터에 접근해서 수정할 때는, 해당 공유 데이터에 LOCK을 걸고 처리가 끝났을때 LOCK을 푸는 방식으로 해결할 수 있음.

- 커널 접근 처리 → 한번에 하나의 CPU만이 커널에 들어갈 수 있게 하는 방법
- 공유 데이터일 경우 → 커널 내부에 있는 각 공유 데이터에 접근할 때마다 그 데이터에 대한 LOCK / UNLOCK을 하는 방법 → 해당 처리가 더 효율적으로 보임

![Untitled](/assets/images/2024-03-09-Operating-system-7-Process-Synchronization--1/Untitled%206.png)

### Critical section

- n개의 프로세스가 공유 데이터를 동시에 사용하기를 원하는 경우
- 각 프로세스의 code segment에는 공유 데이터를 접근하는 코드인 critical section이 존재
- 하나의 프로세스가 critical section에 있을 때 다른 모든 프로세스는 critical section에 들어갈 수 없어야 한다.

![Untitled](/assets/images/2024-03-09-Operating-system-7-Process-Synchronization--1/Untitled%207.png)

critical section(공유 데이터를 접근하는 코드) 이전에 entry section을 넣어서 lock을 걸음 → 여러 프로세스가 동시에 critical section에 들어가는걸 막음.

instruction

### 프로그램적 해결법의 충족 조건

- **Mutual Exclusion (상호 배제)**
    - 프로세스 Pi가 critical section 부분을 수행 중이면 다른 모든 프로세스들은 그들의 critical section에 들어가면 안 된다.
- **Progress (진행)**
    - 어떤 프로세스도 critical section 안에 있지 않은 상태면 들어가게 해주어야 한다.
    - 어떻게 보면 당연한건데 잘못된 코드 구성으로 인해 어떠한 프로세스도 critical section을 수행하고 있지 않은데도 들어가지 못하는 문제가 발생할 수도 있음.
- **Bounded Waiting (유한 대기)**
    - 프로세스가 임계 영역에 들어가려고 요청한 후부터 그 요청이 허용될 때까지 다른 프로세스들이 임계 영역에 들어가는 횟수에 한계가 있어야 한다.
    - 특정 프로세스 입장에서 critical section에 들어가지 못하고 오랫동안 기다리는 현상(Starvation)이 생기지 않게 해야함.
- 가정
    - 모든 프로세스의 수행 속도는 0보다 크다
    - 프로세스들 간의 상대적인 수행 속도는 가정하지 않는다.

# 다음 내용

- Critical section을 해결하는 알고리즘을 소개할 예정.

---

참조

[운영체제 - 이화여자대학교 | KOCW 공개 강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323)

[[운영체제] 멀티 프로세스 환경에서 프로세스간 데이터는 어떻게 전달될까? (tistory.com)](https://hyuuny.tistory.com/153)