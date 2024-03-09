---
title: Operating system (13) Memory Management - 3
excerpt: "Memory Management - 3"

categories:
  - OS

permalink: /os/13/

toc: true
toc_sticky: true
 
date: 2024-03-09 00:00:13
last_modified_at: 2024-03-09
---

## Multilevel Paging and Performance

- Address space가 더 커지면 다단계 페이지 테이블 필요
- 각 단계의 페이지 테이블이 메모리에 존재하므로 logical address의 physical address 변환에 더 많은 메모리 접근 필요
- TLB를 통해 메모리 접근 시간을 줄일 수 있음
- 4단계 페이지 테이블을 사용하는 경우
    - 메모리 접근 시간이 100ms, TLB 접근 시간이 20ns이고
    - TLB hit ratio(TLB를 통해서 주소변환되는 비율)가 98%인 경우 effective memory access time(메모리 접근하는 시간) = 0.98 x 120 + 0.02 x 520 = 120 nanoseconds.
    결과적으로 주소변환을 위해 28ns만 소요

![Untitled](/assets/images/2024-03-09-Operating-system-13-Memory-Management--3/Untitled.png)

테이블 자료구조 특성상 virtual address 크기만큼 엔트리가 만들어져야하고, 사진에서 6,7번 페이지는 없지만 사용이 되지 않기때문에 invalid bit로 표시가 되는것이다.

## Memory Protection

Page table의 각 entry 마다 아래의 bit를 둔다

- Protection bit
    - page에 대한 접근 권한 (read/write/read-only)
    - 어떠한 연산에 대한 접근 권한.
    code영역 같은 경우 내용이 바뀌지 않아야 하고(read-only) data나 stack영역은 바뀌어도 됨.
- Valid-invalid bit
    - “valid”는 해당 주소의 frame에 그 프로세스를 구성하는 유효한 내용이 있음을 뜻함 (접근 허용)
    - “invalid”는 해당 주소의 frame에 **유효한 내용이 없음(프로세스가 그 주소 부분을 사용하지 않거나 해당 페이지가 메모리에 올라와 있지 않고 swap area에 있는 경우**)을 뜻함 (접근 불허)

## Inverted Page Table

![Untitled](/assets/images/2024-03-09-Operating-system-13-Memory-Management--3/Untitled%201.png)

- page table이 매우 큰 이유
    - 모든 process 별로 그 logical address에 대응하는 모든 page에 대해 page table entry가 존재
    - 대응하는 page가 메모리에 있든 아니든 간에 page table에는 entry로 존재
- Inverted page table
    - Page frame 하나당 page table에 하나의 entry를 둔 것 (system-wide)
    - 각 page table entry는 각각의 물리적 메모리의 page frame이 담고 있는 내용 표시 (process-id, process의 logical address)
    - 단점
        - **테이블 전체를 탐색해야 함**
    - 조치
        - associative register(TLE) 사용 (expensive) 순차적인 탐색의 시간 오버헤드를 줄일 수 있음

## Shared Page

![Untitled](/assets/images/2024-03-09-Operating-system-13-Memory-Management--3/Untitled%202.png)

- Shared code
    - Re-entrant Code (=Pure code) 재진입 가능 코드
    - **read-only**로 하여 프로세스 간에 **하나의 code**만 메모리에 올림
    (eg. text editors, compiler, window systems)
    - Shared code는 모든 프로세스의 logical address space에서 동일한 위치에 존재해야 함(**동일한 logical address를 가져야 한다는 것**)
- Private code and data
    - 각 프로세스들은 독자적으로 메모리에 올림
    - Private data는 logical address space의 아무 곳에 와도 무방

## Segmentation

- 프로그램은 의미 단위인 여러 개의 segment로 구성
    - 작게는 프로그램을 구성하는 함수 하나하나를 세그먼트로 정의
    - 크게는 프로그램 전체를 하나의 세그먼트로 정의 가능
    - 일반적으로는 code, data, stack 부분이 하나씩의 세그먼트로 정의됨
- Segment는 다음과 같은 logical unit들임
    - main()
    - function
    - global variables
    - stack
    - symbol table, arrays

## Segmentation Architecture

- Logical address는 다음의 두 가지로 구성
    - segment-number, offset
- Segment table
    - each table entry has:
        - base - starting physical address of the segment(세그먼트 테이블의 시작위치)
        - limit - length of the segment(세그먼트 테이블의 길이(개수))
- Segment-table base register (STBR)
    - 물리적 메모리에서의 segment table의 위치
- Segment-table length register (STLR)
    - 프로그램이 사용하는 segment의 수
        - segment number s is legal if s < STLR

![Untitled](/assets/images/2024-03-09-Operating-system-13-Memory-Management--3/Untitled%203.png)

엔트리에 2가지 정보를 가지고 있음(시작위치, 길이(base, limit))

세그먼트 넘버, 오프셋을 체크해서 길이보다 크면 잘못된 요청(trap: addressing error)

## Segmentation Architecture (Cont.)

- **Protection**
    - 각 세그먼트 별로 protection bit가 있음
    - Each entry:
        - Valid bit = 0 ⇒ illegal segment
        - Read/Write/Execution 권한 bit
- **Sharing**
    - shared segment
    - same segment number
    - segment는 의미 단위이기 때문에 공유(sharing)와 보안(protection)에 있어 paging보다 훨씬 효과적이다
- **Allocation**
    - first fit / best fit
    - external fragmentation 발생
    - segment의 길이가 동일하지 않으므로 가변분할 방식에서와 동일한 문제점들이 발생

![Untitled](/assets/images/2024-03-09-Operating-system-13-Memory-Management--3/Untitled%204.png)

메모리에 올라갈때는 페이지 단위로 쪼개서 올라감 → 기존 segmentation에서 allocation에서의 문제가 없어짐

의미 단위로 하는 일은 segment table에서 해주고 나머지는 page table에서 해줌

세그먼트 테이블에서 주소변환이 일어나면 페이지 테이블에 대한 시작 위치가 나옴 → 세그먼트 하나당 페이지 테이블이 존재