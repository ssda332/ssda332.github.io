---
title: Operating system (10) Deadlock - 1
subtitle: 1
categories: 2
tags: 3
date: 2024-03-09 12:37:32 +0000
last_modified_at: 2024-03-09 12:37:32 +0000
---


## Deadlock이란?

- 일련의 프로세스들이 서로가 가진 자원을 기다리며 block된 상태
- **Resource (자원)**
    - 하드웨어 자원일수도 있고, 소프트웨어 자원일수도 있음.
    - (예) I/O device(두 테잎 드라이버가 한쪽에서 한쪽으로 카피할경우-두 프로세서가 하나의 테잎 드라이버를 점유하고 서로의 테잎 드라이버에 copy하려 할 경우), CPU cycle, memory space, semaphore 등
    - 프로세스가 자원을 사용하는 절차
        - Request(요청), Allocate(획득), Use(사용), Release(반납)

### Deadlock Example

- 두 테잎 드라이버가 한쪽에서 한쪽으로 카피할경우-두 프로세서가 하나의 테잎 드라이버를 점유하고 서로의 테잎 드라이버에 copy하려 할 경우
- Binary semaphores A and B

![Untitled](/assets/images/2024-03-09-Operating-system-10-Deadlock--1/Untitled.png)

## Deadlock 발생의 4가지 조건

### Mutual exclusion (상호 배제)

- 자원을 얻었으면 **독단적으로만** 사용한다는것(매 순간 하나의 프로세스만이 자원을 사용하는 것)

### No preemption (비선점)

- 프로세스는 자원을 스스로 내어놓을 뿐 **강제로 빼앗기지 않음**

### Hold and wait (보유대기)

- 이미 가진 자원을 내놓지 않고 보유하면서 추가적인 자원을 기다리는 것

### Circular wait (순환대기)

- 자원을 기다리는 프로세스간에 **cycle**이 형성되어야 함
- 프로세스 P0, P1, … Pn 이 있을 때
- P0은 P1이 가진 자원을 기다림
- P1은 P2가 가진 자원을 기다림
- Pn-1은 Pn이 가진 자원을 기다림
- Pn은 P0이 가진 자원을 기다림

![Untitled](/assets/images/2024-03-09-Operating-system-10-Deadlock--1/Untitled%201.png)

자원에서 프로세스로 나가는 화살표의 뜻은 **이 자원이 이 프로세스에 속해있다(프로세스가 해당 자원을 가지고 있다.)**

프로세스에서 자원으로 나가는 화살표는 **지금 이 프로세스가 자원을 요청을 한 것이다.(획득하지는 못함. 요청만 한 상태)**

자원 안에 점 : **자원 안에 인스턴스를 뜻함**

![Untitled](/assets/images/2024-03-09-Operating-system-10-Deadlock--1/Untitled%202.png)

![Untitled](/assets/images/2024-03-09-Operating-system-10-Deadlock--1/Untitled%203.png)

- cycle이 있을 경우
    - **자원당 인스턴스가 하나밖에 없으면**, cycle은 **Deadlock**을 의미함
    - **자원당 인스턴스가 여러개**면, 데드락일수도 있고 아닐수도 있음(**possibility**)
    

위 그래프는 cycle이 있는지 진행 방향을 따라가면 여부를 알 수 있다.

왼쪽 그래프는 R2의 자원의 인스턴스가 2개이므로 요청이 들어와도 하나만 내어줄 가능성이 있지만, R2에 대한 cycle도 2개가 있어서 **Deadlock 상황이다.**

오른쪽 그래프는 각 자원(R1, R2)의 인스턴스를 가지는 프로세스들이 모두 존재하고, cycle이 존재한다. **하지만 이 그래프는 Deadlock이 아니다**. R1을 보면 P1이 가질수 있는 인스턴스를 P2와 P3가 모두 가지고 있다. P3가 가지고 있는 인스턴스는 cycle이 있지만 P2가 가지고 있는 인스턴스는 P2가 사용하고 반납할 경우 P1이 사용할 수 있다.

마찬가지로 R2의 경우도 P1이 가지고 있는 인스턴스는 P3가 가지지 못하지만, P4가 반납할경우 해당 인스턴스를 P3가 사용할 수 있다. **cycle이 존재하더라도 Deadlock이 아닌 경우도 있는 것이다.**

## Deadlock의 처리 방법

위에서 1번과 2번은 미연에 방지하는 방법이고(더 강한 방법) 아래 2개는 Deadlock이 생기도록 놔둔다. 현대에서는 Deadlock Ignorance를 채택하고 있다. 미연에 방지하는 방법은 그만큼 overhead가 있기 때문에 비효율적이라고 알려짐.

### Deadlock Prevention

위에서 설명한 Deadlock이 발생하는 4가지 조건중 하나를 원천 차단해서 방지하는 방법

- **Mutual Exclusion**
    - 공유해서는 안되는 자원의 경우 반드시 성립해야 함
    - 한번에 하나의 프로세스만 사용할 수 있는 자원이라면 배제할수 있는 조건은 아님
- **Hold and Wait**
    - 자원을 기다리는 상황에는 다른 어떤 자원도 가지고 있지 않아야 한다.
    - 방법 1. 프로세스 시작 시 모든 필요한 자원을 할당받게 하는 방법(매 시점마다 보유해야될 자원을 한번에 다 받게되면 비효율적이긴 함)
    - 방법 2. 자원이 필요한 경우 보유 자원을 모두 놓고 다시 요청(자원이 필요할 때마다 HOLD 해놓은 자원을 다 놓고 요청함)
- **No Preemption**
    - Process가 어떤 자원을 기다려야 하는 경우 **이미 보유한 자원이 선점됨**( ex: Timer Interrupt 뺏어와서 선점하면 문제없음 )
    - 모든 필요한 자원을 얻을 수 있을 때 그 프로세스는 다시 시작된다.
    - State를 쉽게 save하고 resotre할 수 있는 자원에서 주로 사용(CPU, memory) * cpu는 빼앗긴 시점부터 다시 시작하는게 가능했음
- **Circular Wait**
    - 모든 자원 유형에 할당 순서를 정하여 정해진 순서대로만 자원 할당(예를들어 1번 자원 3번 자원 등 있을때 순번이 낮은 순서대로만 자원 할당)
    - 예를 들어 순서가 3인 자원 Ri을 보유 중인 프로세스가 순서가 1인 자원 Rj을 할당받기 위해서는 우선 R1을 RELEASE해야 한다.

> **Utilization 저하, throughput 감소, starvation 문제(생기지도 않을 Deadlock을 걱정해서 overhead가 생김) 이용률 성능이 낮음**
> 

### Deadlock Avoidance

자원을 요청하는 것에 대한 부가적인 정보를 이용해서 deadlock의 가능성이 없는 경우에만 자원을 할당(프로세스가 시작될때 평생 쓸 자원의 최대량을 알고있다고 가정하고 가능성을 고려해 자원 할당을 함)

가장 단순하고 일반적인 모델은 프로세스들이 필요로 하는 각 자원별 최대 사용량을 미리 선언하도록 하는 방법임

![Untitled](/assets/images/2024-03-09-Operating-system-10-Deadlock--1/Untitled%204.png)

![Untitled](/assets/images/2024-03-09-Operating-system-10-Deadlock--1/Untitled%205.png)

점선 화살표 : 프로세스부터 자원으로 가는 화살표만 있음. 프로세스가 평생 적어도 한번은 해당 자원을 사용할 일이 있다. 라는 뜻

마지막 그래프는 데드락이 아님. P1이 R2를 당장 요청한 것이 아니기 때문

- 자원당 인스턴스가 여러개 있는경우 해당 알고리즘을 쓴다.

![Untitled](/assets/images/2024-03-09-Operating-system-10-Deadlock--1/Untitled%206.png)

![Untitled](/assets/images/2024-03-09-Operating-system-10-Deadlock--1/Untitled%207.png)

Max가 자원을 최대로 쓸 경우를 적어놓은 것.

전체 자원에서(10, 5, 7) 할당된 자원을 빼면 가용할수 있는 자원 수가 나옴(3, 3, 2)

**문제가 생길수 있으면 줄수 있더라도 안주는것!**

![Untitled](/assets/images/2024-03-09-Operating-system-10-Deadlock--1/Untitled%208.png)

P1이 A 1, C 2개를 요청했을 때 → 가용 자원만 가지고도 Max가 처리되기 때문에, 줄수 있음(need - (available - request))

P0이 B 2개 요청했을때 → (need - (available - request))를 충족하지 못함

→ B에게 줄 자원이 있지만 안주게됨

**최대로 요청할수 있는 양이 가용자원보다 많을경우 요청이 받아들여지지 않음!**

P0는 현재 allocation에선 충족되지 않지만 다른 프로세스가 가용자원을 충족하여 수행되고 다 반납했을 경우, 충족될수 있다.

![Untitled](/assets/images/2024-03-09-Operating-system-10-Deadlock--1/Untitled%209.png)

### Deadlock Detection and recovery

- Deadlock 발생은 허용하되 그에 대한 detection 루틴을 두어 deadlock 발견시 recover

![Untitled](/assets/images/2024-03-09-Operating-system-10-Deadlock--1/Untitled%2010.png)

![Untitled](/assets/images/2024-03-09-Operating-system-10-Deadlock--1/Untitled%2011.png)

![Untitled](/assets/images/2024-03-09-Operating-system-10-Deadlock--1/Untitled%2012.png)

- 자원할당 그래프 방식
    - 중간에 자원 부분을 빼버리고 그래프를 다시 그리면 화살표가 Px → Py 이런식으로 되는데 **Py가 가진 자원을 Px가 기다리고 있다**라는 뜻이 됨(좀 더 간결해짐)
    - Deadlock Detection을 할때 wait-for 그래프를 그려놓고 사이클이 있는지 여부를 보면 됨. 위의 그래프는 반드시 Deadlock이 일어난다
- Banker’s Algorithm (자원이 여러개일 경우)
    - Deadlock Detection은 Deadlock 가능성을 신경쓰지 않고 가능한 자원을 전부 우선적으로 할당시켜준다.
    - Deadlock인지 알아보려면 **현재 요청(Request)된 자원이 없는 프로세스는 반납한다고 가정**하고 쌓고 쌓아서 지금 요청된 것들을 처리하는 방식으로 수행
    - P2가 자원 하나를 더 요청했을 경우 → **Deadlock!**

Deadlock이 발견된 경우 Recovery 수행

### Recovery

- Process termination(데드락에 연루된 프로세스를 모두 죽임)
- Resource Preemption(데드락이 없어질때까지 프로세스 하나씩 죽여봄(자원 뺏음))
    - safe state로 rollback하여 process를 restart
    - Starvation 문제
        - 동일한 프로세스가 계속해서 victim으로 선정되는 경우
        - cost factor에 rollback 횟수도 같이 고려

### Deadlock Ignorance

- Deadlock을 시스템이 책임지지 않음
- UNIX를 포함한 대부분의 OS가 채택
- 운영체제가 책임지지 않으면 사람이 수동으로 프로세스를 죽이든지 해서 처리함.