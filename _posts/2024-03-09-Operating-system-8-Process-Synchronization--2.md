---
title: Operating system (8) Process Synchronization - 2
excerpt: "Process Synchronization - 2"

categories:
  - OS

permalink: /os/8/

toc: true
toc_sticky: true
 
date: 2024-03-09
last_modified_at: 2024-03-09
---

## Algorithm 1

![Untitled](/assets/images/2024-03-09-Operating-system-8-Process-Synchronization--2/Untitled.png)

- turn : critical section에 들어갈 변수가 누구인지 알려주는 변수 (0이면 0번 프로세스 차례)
- 자기 차례가 아닐경우 while문을 무한반복 하면서 자기차례를 기다림
- 자기 차례가 되면 그때 critical section으로 들어감
- 모든 Instruction이 끝나면 turn을 다음 차례로 바꿔줌
- **Mutual exclusion은 만족함. 하지만 progress는 만족하지 못함**
    - 위의 코드는 반드시 교대로 들어가도록 코딩되어 있음.
    - critical section을 들어가려는 프로세스의 빈도가 균일하지 않을경우 문제가 발생

## Algorithm 2

![Untitled](/assets/images/2024-03-09-Operating-system-8-Process-Synchronization--2/Untitled%201.png)

- critical section에 들어가기 전에 본인 flag(flag[i])를 true로 함
- 상대방 flag 체크하고 true상태이면 무한반복하면서 기다림
- 들어온 프로세스가 없으면 while문을 나와서 critical section을 수행하고 자신의 flag를 false처리해서 instruction을 끝냄
- **아무도 critical section에 들어가 있지 않지만 수행되지 않는 progress문제는 동일하게 있음**

## Algorithm 3

![Untitled](/assets/images/2024-03-09-Operating-system-8-Process-Synchronization--2/Untitled%202.png)

- 앞에 설명한 turn과 flag를 전부 사용함
- 위의 설명된 문제를 전부 해결할 수 있음. 또한 충족 조건 3가지를 전부 충족함
- **조건을 만족하지 않을경우 while문을 계속 돌게 되는데 아무런 의미 없이(비효율적) 체크했던 조건을 체크하면서 본인의 CPU 할당 시간을 다 쓰게 되는 문제가 있음 (Busy Waiting == spin lock)**

![Untitled](/assets/images/2024-03-09-Operating-system-8-Process-Synchronization--2/Untitled%203.png)

- 데이터를 읽고 쓰는것을 하나의 Instruction으로 처리할 수 없기 때문에 위의 문제가 생겼었음 → CPU를 뺏길때는 Instruction 단위로 일어나기 때문에 하나의 Instruction 안에 해결이 된다면 문제 해결이 가능함
- Lock을 걸고 푸는게 atomic하게 해결됨

![Untitled](/assets/images/2024-03-09-Operating-system-8-Process-Synchronization--2/Untitled%204.png)

![Untitled](/assets/images/2024-03-09-Operating-system-8-Process-Synchronization--2/Untitled%205.png)

- 공유 자원을 획득하고 반납하는 것을 세마포어가 처리를 해줌
- P연산은 락을 거는 과정(세마포어 변수값을 줄임)이고 V연산은 락을 푸는 과정(세마포어 변수값을 늘림, 원상태로 회복)이다.
- P연산을 할 때, S값이 0이하이면 자원을 다 가져갔다는 뜻이므로 기다림.
- 누군가 반납을 했을경우 S값을 1 빼고 획득
- 사용이 끝나면 V연산으로 반납(자원을 내어놓는것)
- P와 V는 atomic하게 수행됨
- **여기서도 busy waiting 문제는 생김(spin lock)**
- **위의 예처럼 사용자가 일일히 코딩하는게 아니라 추상 자료형을 제공해주고, 프로그래머는 세마포어를 통해서 프로그래밍을 하면 훨씬 간단하게 작성할 수 있음.**

![Untitled](/assets/images/2024-03-09-Operating-system-8-Process-Synchronization--2/Untitled%206.png)

![Untitled](/assets/images/2024-03-09-Operating-system-8-Process-Synchronization--2/Untitled%207.png)

- P연산 : 자원에 여분이 없다면 구조체에 프로세스를 연결시키고(add this process to S.L) block 상태로 들어감
- V연산 : 자원이 끝날때 해당 자원을 기다리면서 잠들어있는 프로세스가 있으면 wakeup해주는 연산이 같이 들어감
- 기존 S는 자원을 단순히 세기만 했는데 여기서는 음수가 되버리면 누군가 자원을 기다리고 있다는 의미, 양수이면 자원의 여분이 있기때문에 깨우지 않아도 된다.

### Which is better?

- Busy-wait v.s Block/wakeup
- 상식적으로 Block/wakeup을 사용하는게 효율적임
- 그러나 Block/wakeup 방식도 overhead가 있음. 어떤 프로세스의 상태를 ready에서 sleep으로 바꿔야하기 때문, 그리고 공유데이터의 여분이 생기면 wakeup을 해줘야함
    - Critical section의 길이가 긴 경우 Block/Wakeup이 적당
    - Critical section의 길이가 매우 짧은 경우 Block/wakeup 오버헤드가 busy-wait 오버헤드보다 더 커질 수 있음
    - 일반적으로는 Block/wakeup 방식이 더 좋음

### Two Types of Semaphores

- **Binary semaphore (=mutex)**
    - 0 또는 1 값만 가질 수 있는 semaphore
    - 주로 mutual exclusion (lock/unlock)에 사용
- **Counting semaphore**
    - 도메인이 0 이상인 임의의 정수값
    - 주로 resource counting에 사용
    

![Untitled](/assets/images/2024-03-09-Operating-system-8-Process-Synchronization--2/Untitled%208.png)

- Deadlock
    - 상대방이 가진것을 기다리면서 자기가 가진것을 내어놓지 않아 상대와 자신이 무한정 기다리는 현상을 Deadlock 이라고함
    - 해결법 → 세마포어를 얻는 순서를 똑같이 바꿔야함 P1의 과정이 1.Q 2.S 로 되어있는것을 뒤바꿔야함 → 프로그래머가 유의해서 코딩해야함
- Starvation
    - Deadlock도 일종의 Starvation으로도 볼수 있음
    - 참고) 철학자 식탁 문제

# 다음 내용

- Process Syncronization과 관련된 알고리즘을 소개할 예정.

---

참조

[운영체제 - 이화여자대학교 | KOCW 공개 강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323)