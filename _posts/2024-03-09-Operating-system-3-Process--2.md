---
title: Operating system (3) Process - 2
excerpt: "Process - 2"

categories:
  - OS

permalink: /os/3/

toc: true
toc_sticky: true
 
date: 2024-03-09
last_modified_at: 2024-03-09
---

### Thread : CPU를 수행하는 단위, 프로세스 내부에 CPU 수행 단위가 여러개 있는것

- Thread의 구성
    - program counter
    - register set
    - stack space

위의 구성요소들은 **Thread가 독립적으로 가지고 있는 요소**들이다(해당 요소는 CPU 수행과 관련된 부분). 밑에 나오는 요소는 Thread가 다른 동료 Thread와 공유하는 부분이다.

- Thread가 동료 thread와 공유하는 부분(=task)
    - code section
    - data section
    - OS resources
- 전통적인 개념의 heavyweight process는 하나의 thread를 가지고 있는 task로 볼 수 있다.
- 다중 스레드로 구성된 태스크 구조에서는 **하나의 서버 스레드가 blocked(waiting)상태인 동안에도 동일한 태스크 내의 다른 스레드가 실행(running)되어 빠른 처리를 할 수 있다**(웹 브라우저는 여러 개의 쓰레드로 구성되어 있음 → 그림을 불러오는 동안 텍스트를 다른 쓰레드가 브라우저에 전달해줌 → **빠른 응답성 제공**)
- 스레드를 사용하면 병렬성을 높일 수 있다.

![Untitled](/assets/images/2024-03-09-Operating-system-3-Process--2/Untitled.png)

![Untitled](/assets/images/2024-03-09-Operating-system-3-Process--2/Untitled%201.png)

### Thread의 장점

- 응답성 (Responsiveness)
    - 웹 브라우저가 쓰레드를 여러개 가지고 있으면 하나의 쓰레드가 멀리 있는 서버에서 자료(html문서, 이미지 등)요청을 할때 요청한 쓰레드만 blocked 처리가 되고 다른 쓰레드는 요청 작업을 지속할 수 있다.
- 자원의 공유 (Resource Sharing)
    - 똑같은 일을 하는 프로그램이 여러개가 있으면 → 하나의 프로세스를 만들고 여러 쓰레드로 만들게되면 code나 data, 프로세스의 자원 등을 공유할 수 있음
- 경제성 (Economy)
    - 프로세스를 하나 더 만드는것, 문맥 교환이 일어날때 → **오버헤드가 큼**(PCB 저장, cache memory flush 같은 추가 작업 일어나기 때문)
    - 한 프로세스에 쓰레드를 하나 더 만드는것 → **상대적으로 오버헤드 크지 않음**
- CPU가 여러개 있는 환경(MP Architectures)에서 쓰레드를 여러개 뒀을때 유용성
    - 여러개의 쓰레드가 여러개의 CPU에서 병렬적으로 수행이 가능
    

### 쓰레드의 구현 (Implementation of Threads)

어떠한 쓰레드는 커널의 지원을 받고있고 어떤 쓰레드는 라이브러리의 지원을 받고 있다. 전자를 커널 쓰레드, 후자를 유저 쓰레드라고 부른다.

- 커널 쓰레드 - 커널이 쓰레드가 여러개 있다는걸 아는것, ‘쓰레드 → 쓰레드’ 과정을 커널이 CPU 스케줄링 하듯이 넘겨줌
- 유저 쓰레드 - 커널은 쓰레드가 여러개 있다는걸 모르고, 사용자 프로세스가 모든 쓰레드를 관리하는 것
- 실시간 쓰레드
- [Thread의 종류(하드웨어 Thread, 커널 레벨 Thread, 유저 레벨 Thread) (velog.io)](https://velog.io/@chanyoung1998/%EC%8A%A4%EB%A0%88%EB%93%9C%EC%9D%98-%EC%A2%85%EB%A5%98%ED%95%98%EB%93%9C%EC%9B%A8%EC%96%B4-%EC%8A%A4%EB%A0%88%EB%93%9C-OS-%EC%8A%A4%EB%A0%88%EB%93%9C-%EC%9C%A0%EC%A0%80-%EB%A0%88%EB%B2%A8-%EC%8A%A4%EB%A0%88%EB%93%9C)
- [[OS] Race Condition & 뮤텍스 & 세마포어 (velog.io)](https://velog.io/@supssson/Race-Condition-%EB%AE%A4%ED%85%8D%EC%8A%A4-%EC%84%B8%EB%A7%88%ED%8F%AC%EC%96%B4)

### TCB

```
운영체제 책에는 이렇게 설명되어 있지만, 의문이 든다."프로세스가 여러개의 스레드를 가지면 PCB 하나로 어떻게 저장하지? 각 Thread의 정보를 저장해야 할 텐데."이렇게 생각해서 알아본 것이 TCB이다.PCB는 Process ID와 상태, 우선순위, 메모리 정보 등을 저장한다. 멀티스레드가 아닌 멀티프로세스 환경에서는 PCB가 PC와 Register Set 정보도 포함한다. 여기서는 멀티스레드 환경이라 가정한다.TCB는 Thread별로 존재하는 자료구조이며, PC와 Register Set(CPU 정보), 그리고 PCB를 가리키는 포인터를 가진다.
```

출처:

[https://teraphonia.tistory.com/802](https://teraphonia.tistory.com/802)

[Binceline:티스토리]

![Untitled](/assets/images/2024-03-09-Operating-system-3-Process--2/Untitled%202.png)

- TCB는 PCB보다 적은 데이터를 가지는 자료구조

---

참조

[운영체제 - 이화여자대학교 | KOCW 공개 강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323)