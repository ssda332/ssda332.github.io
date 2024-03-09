---
title: Operating system (9) Process Synchronization - 3
subtitle: 1
categories: 2
tags: 3
date: 2024-03-09 12:37:14 +0000
last_modified_at: 2024-03-09 12:37:14 +0000
---


## Classical Problems of Synchronization

### Bounded-Buffer Problem (Producer-Consumer Problem)

![Untitled](/assets/images/2024-03-09-Operating-system-9-Process-Synchronization--3/Untitled.png)

- 버퍼의 크기가 유한한 환경에서의 생산자-소비자 문제이다.
- 위의 그림과 같이 공유데이터에 Lock을 걸고 buffer 조작 후 락을 풀고 buffer 수를 증가시킨다.
- Mutual exclusion :  같은 생산자끼리 같은 empty 버퍼에 값을 동시에 넣으려고 하면 문제가 생김. 소비자도 마찬가지로 같은 full 버퍼에서 데이터를 뺴려고 하면 문제가 생김.
- resource count : 버퍼의 개수가 n개라면 full 버퍼의 개수가 n개일때 생산자는 데이터를 입력하지 못하고 empty 버퍼의 개수가 n개일때 소비자는 데이터를 꺼내지 못함
- 따라서 해당 문제에서 해결해야될것은 2개가 있음
    - Lock 목적의 세마포어
        - 생산자는 빈 버퍼에 데이터를 넣고 소비자는 채워진 버퍼에서 데이터를 꺼내가게 되는데 같은 버퍼 공간을 중복해서 Instruction이 일어날수 있기 때문에 Lock을 걸고 Instruction을 수행한다.
    - Resource Count 목적의 세마포어
        - full/empty 버퍼의 수를 표시하는 세마포어로 모든 Instruction이 끝날 때 각각의 버퍼를 증가시켜야함
        

![Untitled](/assets/images/2024-03-09-Operating-system-9-Process-Synchronization--3/Untitled%201.png)

- 세마포어 변수는 3개가 필요
    - lock을 해주는 mutex 변수
    - full buffer 개수 세는 변수
    - empty buffer 개수 세는 변수
- Producer
    1. item x 만듦
    2. empty 버퍼를 획득 : P(empty) → empty—
    3. 버퍼 전체에 Lock : P(mutex)
    4. 버퍼에 x 집어넣음
    5. Lock 해제 : V(mutex)
    6. full 버퍼 (소비자 입장에서의 자원)을 증가 : V(full) → full++
- Consumer
    1. full 버퍼를 획득 : P(full) → full—
    2. 버퍼 전체에 Lock : P(mutex)
    3. 버퍼에 y 꺼냄
    4. Lock 해제 : V(mutex)
    5. empty 버퍼 (소비자 입장에서의 자원)을 증가 : V(empty) → empty++
    6. y 소비

### Readers-Writers Problem

![Untitled](/assets/images/2024-03-09-Operating-system-9-Process-Synchronization--3/Untitled%202.png)

- 프로세스로는 읽는 프로세서와 쓰는 프로세스가 있고 공유데이터로 DB가 있다.
- 위의 Bounded-Buffer 문제에서와 다르게 write는 동시에 하면 안되지만 read는 동시에 해도 됨
- Writer는 항상 아무도 없을때(배타적, 독점적)으로 수행되어야 함

![Untitled](/assets/images/2024-03-09-Operating-system-9-Process-Synchronization--3/Untitled%203.png)

- small db는 DB에 lock를 걸어주는 세마포어
- reader도 쉽게 구현하려면 writer랑 똑같이 해주면 됨. 하지만 비효율적(읽는 작업이 동시에 이루어지지 못함)
- 어쨌든 lock을 안걸고 읽으면 writer가 그냥 써버리니까 lock은 걸어야함
- 다른 reader가 왔을때 읽을수 있도록 readcount 변수를 둠
- 최초의 reader만 DB에 lock을 거는 P(db)를 수행함으로써 read 작업만 동시에 수행되도록 할 수 있다.
- readcount 변수도 공유 변수이기 때문에 lock을 걸 필요가 있고 mutex라는 세마포어 변수가 필요함
- 마지막으로 읽고 빠져나가는 프로세스라면(readcount == 0) DB에 lock을 풀어줌 → V(db)
- Reader가 엄청 많을 경우 Starvation 발생 가능 → 코드 개선 필요(reader의 접근을 막고 writer 수행되게)

### Dining-Philosophers Problem

![Untitled](/assets/images/2024-03-09-Operating-system-9-Process-Synchronization--3/Untitled%204.png)

- 밥을 먹으려면 왼쪽 오른쪽 젓가락을 잡아야함
- 젓가락은 1로 초기화 되어있기 때문에 양옆의 사람중 한명이 젓가락을 잡아서 식사를 할경우 해당 사람은 식사를 할 수 없다.
- Deadlock 가능성이 있음 → 모든 철학자가 동시에 왼쪽 젓가락을 잡았을때 아무도 오른쪽 젓가락을 잡지 못함. 위의 코드는 deadlock이 발생하는 아주 잘못된 코드임

### 해결방안

- 4명의 철학자만이 테이블에 동시에 앉을 수 있도록 한다.
- 젓가락을 두 개 모두 집을 수 있을 떄에만 젓가락을 잡을 수 있게 한다.
- 비대칭
    - 짝수(홀수)철학자는 왼쪽(오른쪽) 젓가락부터 잡도록

![Untitled](/assets/images/2024-03-09-Operating-system-9-Process-Synchronization--3/Untitled%205.png)

- i = 5명의 철학자 번호
- semaphore 변수 self, mutex(lock)
- 공유 변수 : state[5] 상태 : thinking, hungry, eating

1. pickup(i) 실행 - 철학자가 젓가락을 집기 시작함
    1. 상태를 건드리기 때문에 lock을 처음에 걸음
    2. 자신의 상태를 hungry로 바꿈
    3. 철학자가 젓가락을 온전히 잡을수 있는 상태인지 test
        1. 왼쪽 오른쪽 철학자가 밥먹고있지않고, 내가 배고픈 상태이면 eating 상태로 바꾸고 젓가락 잡을수 있는 권한을 줌.
        2. **기존엔 semaphore를 5명이면 5로 초기화하고 젓가락을 집고 4로 줄이는 방식으로 했었지만, 각각에 대해서 0으로 초기화후 상태 체크후 1로 바꾸는(V(self[i]) 과정을 진행해서 이전과 P와 V의 실행이 뒤바뀜(세마포어 철학에 맞지 않는다고 함.. 뒤에 설명할 monitor 코드가 더 적절함)**
        3. 젓가락을 집었으면 다른 철학자가 젓가락을 집는 과정을 진행할 수 있게 lock을 풀고, 만약 젓가락을 집을수 없는 상황이었다면 P(self[i]) 단계에서 젓가락을 집을수 있을때까지 기다리게 된다.
    4. 젓가락을 집었으면 pickup을 빠져나와 eat()을 수행한다(밥을 먹는다)
    5. 다 먹었으면 putdown(i)를 수행한다(젓가락을 내려놓는다)
        1. 이 과정에서 양옆에 철학자가 기다리고 있을수 있으므로 각각의 철학자에 대해 test를 수행함으로써 세미포어 변수를 변경해 젓가락을 집을 수 있게 해준다.
    6. eating 상태에서 초기 상태인 thinking 상태로 되돌아온다.
    

![Untitled](/assets/images/2024-03-09-Operating-system-9-Process-Synchronization--3/Untitled%206.png)

- 세마포어는 P연산과 V연산을 통해 프로그래머에게 코딩을 비교적 쉽게 만들어주긴 했으나 그럼에도 불구하고 문제가 생겼을 때 버그를 잡기가 쉽지 않음.
- Monitor : 동시 수행중인 프로세스 사이에서 abstract data type의 안전한 공유를 보장하기 위한 high-level synchronization construct

![Untitled](/assets/images/2024-03-09-Operating-system-9-Process-Synchronization--3/Untitled%207.png)

모니터는 프로그래밍 언어 차원에서 **공유 데이터에 접근하는 문제를 자동으로 해결해주면서 프로그래머에게 부담을** 줄여준다.

기존 세마포어 방식에서는 process syncronization 처리를 하기 위해 critical section에 접근할 때마다 lock을 걸어주게 되었는데 모니터는 active한 프로세스만 모니터 안의 코드를 실행할수 있도록 모니터 자체적으로 제어해주기 때문에 프로그래머가 따로 그러한 처리를 할 필요가 없다.

모니터 내부에서 active되어서 Instruction을 처리하는 프로세스가 있다고 치자. 그러다 CPU를 다른 프로세스에 뺏겼다고 하더라도 모니터 안에는 기존 active 되어있는 프로세스는 다시 CPU를 얻기까지 기다리고 있고 CPU를 얻은 다른 프로세스는 모니터 밖에 있는 queue에서 기다리게 되는 것이다.

**요약하면 모니터에서는 lock을 걸고 풀고 하는게 원칙적으로 필요하지 않고 모니터가 알아서 제어해준다는 것이다.**

![Untitled](/assets/images/2024-03-09-Operating-system-9-Process-Synchronization--3/Untitled%208.png)

![Untitled](/assets/images/2024-03-09-Operating-system-9-Process-Synchronization--3/Untitled%209.png)

- **wait()**
- 어떤 프로세스가 특정 조건을 만족하지 않아서 작업이 오래 기다려야 할 때, 해당 프로세스를 잠들게 하기 위해 호출하는 것.
- **signal()**
    - 누군가가 x를 다 쓰고 빠져나가서 잠들어있는 프로세스가 있으면 그것중에 하나를 깨우라고 명령하는 것.
    
    해
    

![Untitled](/assets/images/2024-03-09-Operating-system-9-Process-Synchronization--3/Untitled%2010.png)

기존 세마포어 방식에선 공유 버퍼에 접근할 때, 생산자든 소비자든 lock을 걸어 다른 생산자나 소비자가 접근하지 못하도록 했지만, 모니터에서는 그러한 코드가 필요없음.

- condition variable
    - full : 내용이 들어있는 버퍼를 기다리는 프로세스를 줄세워 놓는곳
    - empty : 빈 버퍼를 기다리는 프로세스를 줄세워 놓는곳

![Untitled](/assets/images/2024-03-09-Operating-system-9-Process-Synchronization--3/Untitled%2011.png)

[🔒 Java 로 동기화를 해보자! (techcourse.co.kr)](https://tecoble.techcourse.co.kr/post/2021-10-23-java-synchronize/)