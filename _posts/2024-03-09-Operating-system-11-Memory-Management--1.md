---
title: Operating system (11) Memory Management - 1
excerpt: "Memory Management - 1"

categories:
  - OS

permalink: /os/11/

toc: true
toc_sticky: true
 
date: 2024-03-09
last_modified_at: 2024-03-09
---


**메모리 : 주소를 통해 접근하는 매체**

### Logical address(=virtual address, 논리적 주소)

- **프로세스마다 독립적으로 가지는 주소 공간**
- 프로세스마다 0번지부터 시작
- **CPU가 보는 주소**는 logical address임(밑의 그림 참조)

### Physical address

- 물리적인 주소, 메모리에 실제 올라가는 위치

### Symbolic Address

- 프로그래밍할땐 숫자로 된 주소를 사용하지 않고 symbol로 된 주소를 사용함(함수 이름, 변수 이름)

> **주소 바인딩** : **프로그램이 물리적인 메모리 어디에 올라갈지 메모리 주소를 결정하는 것**
Symbolic Address(프로그래머가 정한 변수명) →(컴파일) Logical Address →(이 시점이 언제인가?) Physical Address
> 

## 주소 바인딩 (Address Binding)

![Untitled](/assets/images/2024-03-09-Operating-system-11-Memory-Management--1/Untitled.png)

소스 코드를 프로그래머가 작성하면 Symbolic Address로 소스 코드가 작성된다.

이후 컴파일되어서 실행 파일이 생기면 Logical address로 바뀐다.

이 파일이 실행이 되려면 물리적인 메모리에 올라가야 하고, 물리적인 메모리에 주소가 결정되는것을 **주소 바인딩**이라고 부름.

### Compile time binding

지금의 컴퓨터에서는 해당 바인딩 방법을 사용하지 않음.

컴파일 시에 물리적인 주소가 결정되기 때문에 논리 주소 번지수 그대로 올라가게 됨.

위에 그림을 보면 물리 주소가 논리 주소와 같은 것을 볼 수 있음.

- 물리적 메모리 주소가 컴파일 시 알려짐
- 시작 위치 변경시 재컴파일
- 컴파일러는 절대 코드(absolute code) 생성

- > 물리적 메모리 위치를 변경하려면 컴파일을 다시 해야 하며, 잘 사용하지 않는 기법
- > 만약 이미 다른 프로세스가 메모리를 차지하고 있는 경우도 존재할 수 있습니다.
- > 즉 하나의 프로세스만을 사용하는 것이 확실 할 때만 사용합니다.

### Load time binding

실행 시점부터 올린다는 뜻인데, 위에 그림과 같이 만약 해당 시점에 500번째부터 비어있으면 그 번지수부터 올린다는 뜻

- Loader의 책임하에 물리적 메모리 주소 부여
- 컴파일러가 재배치가능코드(relocatable code)를 생성한 경우 가능

물리적 메모리 주소와 논리적 메모리 주소를 분리하여 컴파일 타임 바인딩과는 다르게 multiprogramming이 가능합니다.

### Execution time binding (=Runtime binding)

실행 시점부터 주소를 올리는 것은 Load time binding과 같지만, 주소가 바뀔수도 있음. 300 → 700 (실행 도중에 바뀜)

지금 사용하는 컴퓨터는 런타임 바인딩을 지원하고 있음

- 수행이 시작된 이후에도 프로세스의 메모리 상 위치를 옮길 수 있음
- **CPU가 주소를 참조할 때마다 binding을 점검 (address mapping table)하기 때문에 하드웨어적인 지원이 필요 (e.g., base and limit register, MMU)**

**CPU는 논리 주소를 바라보고 작업을 하며, Instruction을 수행할 때마다 주소 변환을 요청한다(MMU).**

## Memory-Management Unit (MMU)

주소변환을 지원해주는 **하드웨어**다.

(논리 주소를 물리 주소로 매핑해 주는 하드웨어 장치)

- 가정 : 프로그램의 논리 주소가 통째로 물리 주소에 올라간다.

### MMU scheme

- 사용자 프로세스가 CPU에서 수행되며 생성해내는 모든 주소값에 대해 **base register(=relocation register)**의 값을 더 한다

### user program

- logical address만을 다룬다.
- 실제 physical address를 볼 수 없으며 알 필요가 없다.

![Untitled](/assets/images/2024-03-09-Operating-system-11-Memory-Management--1/Untitled%201.png)

1. cpu가 메모리 346번지에 있는 데이터를 달라고 요청
2. MMU에서 Register 2개에서 주소변환이 이뤄짐
    1. **base register(relocation register)**에서 physical memory의 시작 위치(14000)을 저장해놓음
    2. **limit register**에서 프로그램의 크기를 담아서 프로그램이 **악의적인 프로그램이라서 프로그램 밖의 다른 주소를 달라고 할 경우를 체크**해줌(trap: addressing error)
    3. 논리주소에 시작위치를 더해서 물리 주소 값을 얻어냄

## Dynamic Loading(동적 로딩)

메모리에다가 동적으로 올린다는 것을 뜻함

운영체제가 직접 해주는게 아니라 프로그래머가 직접 제어하는것(현대에선 섞어 쓰기도 함)

- 프로세스 전체를 메모리에 미리 다 올리는 것이 아니라 해당 루틴이 불려질 때 메모리에 load하는것
- memory utilization의 향상
- 가끔씩 사용되는 많은 양의 코드의 경우 유용(예: 오류 처리 루틴)
- 운영체제의 특별한 지원 없이 프로그램 자체에서 구현 가능 (OS는 라이브러리를 통해 지원 가능)
- 뒤에 나올 Paging은 프로그래머가 직접 제어하지 않고 운영체제가 맡음(Dynamic loading을 Paging이라고 하기도 함. 섞어씀)

### Overlays

- 메모리에 프로세스의 부분 중 실제 필요한 정보만을 올리는 것(다이나믹 로딩과 똑같은 얘기)
- 프로세스의 크기가 메모리보다 클 때 유용
    - 초창기 시스템에서는 메모리 크기가 매우 작았기 때문에 프로세스를 잘라서 프로그래머가 수작업으로 구현하였음(운영체제의 지원 없음)

### Swapping

프로세스를 일시적으로 메모리에서 backing store로 쫓아내는 것

![Untitled](/assets/images/2024-03-09-Operating-system-11-Memory-Management--1/Untitled%202.png)

- **Backing store (=swap area)**
    - **디스크**
        - 많은 사용자의 프로세스 이미지를 담을 만큼 충분히 빠르고 큰 저장 공간
- Swap in / Swap out
    - 일반적으로 중기 스케줄러(swapper)에 의해 swap out 시킬 프로세스 선정
    - priority-based CPU scheduling algorithm
        - priority가 낮은 프로세스를 swapped out 시킴
        - priority가 높은 프로세스를 메모리에 올려놓음
    - Compile time 혹은 load time binding에서는 원래 메모리 위치로 swap in 해야 함(swapping의 효과를 보기 힘듦)
    - Execution time binding에서는 추후 빈 메모리 영역 아무 곳에나 올릴 수 있음
    - swap time은 대부분 transfer time (swap되는 양에 비례하는 시간)

**swap out의 의미는 일부 페이지가 쫓겨나는 경우도 포함이지만 원칙적인 의미는 프로세스 전체가 쫓겨나는것을 뜻하기 때문에 중기 스케줄러랑 같이 설명함**

## Dynamic Linking

컴파일 후 링킹(컴파일된 파일들을 묶어서 하나의 프로그램으로 만드는 것)해서 실행파일을 만듦.

### static linking

- 라이브러리가 프로그램의 실행 파일 코드에 포함됨
- 실행 파일의 크기가 커짐
- 동일한 라이브러리를 각각의 프로세스가 메모리에 올리므로 메모리 낭비 (eg. printf 함수의 라이브러리 코드)

### dynamic linking

내 코드 안에 실행파일을 만들때 라이브러리를 집어넣는게 아니라 라이브러리를 별도의 파일로 두고 라이브러리를 찾을 수 있는 포인터만 실행파일 안에 두는것을 뜻함

- 라이브러리가 실행시 연결(link)됨
- 라이브러리 호출 부분에 라이브러리 루틴의 위치를 찾기 위한 stub이라는 작은 코드를 둠
- 라이브러리가 이미 메모리에 있으면 그 루틴의 주소로 가고 없으면 디스크에서 읽어옴
- 운영체제의 도움이 필요

ex) 자바의 **`import`** 문으로 추가되는 라이브러리는 컴파일 타임에 참조되어 컴파일러에 의해 필요한 클래스의 위치를 알려주는 역할을 합니다. 하지만, 실제 라이브러리 코드가 실행되는 프로그램의 메모리로 로드되는 것은 런타임에 이루어지며, 이 과정을 "동적 링킹(Dynamic Linking)"이라고 합니다.

(shared library, dll)

## Allocation of Physical Memory(물리 주소 배정)

주소 바인딩할때 앞서 말한 방법들은 한꺼번에 올리는 **연속 할당 방법**이었음.

- 메모리는 일반적으로 두 영역으로 나뉘어 사용
    - OS 상주 영역
        - interrupt vector와 함께 낮은 주소 영역 사용
    - 사용자 프로세스 영역
        - 높은 주소 영역 사용
- 사용자 프로세스 영역의 **할당 방법**
    - **Contiguous allocation(연속 메모리 할당)** : 각각의 프로세스가 메모리의 연속적인 공간에 적재되도록 하는 것
    - Fixed partition allocation
        - 프로그램이 들어갈 사용자 메모리 영역을 미리 파티션으로 나누어 놓는것(물리적 메모리를 몇 개의 영구적 분할(partition)로 나눔
        - 분할의 크기가 모두 동일한 방식과 서로 다른 방식이 존재
        - 분할당 하나의 프로그램 적재
        - 융통성이 없음
            - 동시에 메모리에 load되는 프로그램의 수가 고정됨
            - 최대 수행 가능 프로그램 크기 제한
            - Internal fragmentation 발생 (external fragmentation도 발생)
    - Variable partition allocation
        - 파티션으로 미리 나눠놓지 않는것
        - 가변 분할 방식을 사용하더라도 프로그램의 크기가 일정하지 않기 때문에 프로그램이 종료된 곳에 다른 프로그램이 들어가지 못한다면 외부 조각으로 남아있게 됨
        - 기술적 관리 기법 필요
        - External fragmentation 발생(외부조각 발생)
    
    ![Untitled](/assets/images/2024-03-09-Operating-system-11-Memory-Management--1/Untitled%203.png)
    
    외부 조각: 프로그램의 크기가 커서 사용이 안된 조각
    
    내부 조각: 프로그램한테 할당이 된 공간이지만 사용되지 않는 빈 공간
    
    - Hole
        - 가용 메모리 공간
        - 다양한 크기의 hole들이 메모리 여러 곳에 흩어져 있음
        - 프로세스가 도착하면 수용가능한 hole을 할당
        - 운영체제는 다음의 정보를 유지
            - a) 할당 공간 b) 가용 공간(hole)
    - **Noncontiguous allocation(불연속 메모리 할당)** : 하나의 프로세스가 메모리의 여러 영역에 분산되어 올라갈 수 있음
        - **Paging**
            - **주소 변환은 page별로 해야되기 때문에 address binding이 복잡해짐**
            - 프로세스의 논리 주소 공간(virtual memory)를 동일한 사이즈의 page 단위로 나눔
            - 가상 메모리의 내용이 page 단위로 noncontiguous하게 저장됨
            - 일부는 backing storage에, 일부는 physical memory에 저장
            - Internal fragmentation 생길수 있음. 프로그램의 크기가 페이지 크기의 배수가 되라는 법은 없음
        - **Segmentation**
            - 프로그램의 주소공간을 같은 크기로 자르는게 아니라 의미있는 단위로 자르는 것(code, data, stack)
            - 크기가 균일하지는 않음
        - **Paged Segmentation**

### Dynamic Storage-Allocation Problem

가변 분할 방식에서 사이즈 n인 요청을 만족하는 가장 적절한 hole을 찾는 문제

- First-fit
    - Size가 n이상인 것 중 최초로 찾아지는 hole에 해당
- Best-fit
    - Size가 n이상인 가장 작은 hole을 찾아서 할당
    - Hole들의 리스트가 크기순으로 정렬되지 않은 경우 모든 hole의 리스트를 탐색해야함
    - 많은 수의 아주 작은 hole들이 생성됨
- Worst-fit
    - 가장 큰 hole에 할당
    - 역시 모든 리스트를 탐색해야 함
    - 상대적으로 아주 큰 hole들이 생성

> **First-fit과 best-fit이 worst-fit보다 속도와 공간 이용률 측면에서 효과적인 것으로 알려짐 (실험적인 결과)**
> 

### compaction

- 외부 조각으로 생기는 hole을 한군데로 밀어서 아주 큰 hole을 만들면 hole의 크기가 작아서 사용이 안되는 외부 조각 문제를 해결할 수 있음(디스크 조각모음)
- 매우 비용이 많이 드는 방법
- 최소한의 메모리 이동으로 compaction하는 방법 (매우 복잡한 문제)
- compaction은 프로세스의 주소가 실행 시간에 동적으로 재배치 가능한 경우(runtime binding)에만 수행될 수 있다.