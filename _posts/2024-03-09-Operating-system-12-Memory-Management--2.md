---
title: Operating system (12) Memory Management - 2
subtitle: 1
categories: 2
tags: 3
date: 2024-03-09 12:38:10 +0000
last_modified_at: 2024-03-09 12:38:10 +0000
---


> 물리적인 메모리를 관리하는 기법으로 OS 상주 영역이 있고 사용자 프로세스 영역이 있는데 사용자 프로세스 영역에 연속적으로 할당하는 방법과 불연속적으로 할당하는 방법이 있었다. 연속적 할당은 시작 주소만 알고 있으면 쉽게 할당이 가능했다.(Dynamic Relocation)
하지만 실제로는 불연속적으로, 그리고 여러 방법으로 할당되게 된다. 해당 장에선 그 내용을 다룰 것이다.
> 

## Paging

![Untitled](/assets/images/2024-03-09-Operating-system-12-Memory-Management--2/Untitled.png)

프로그램을 구성하는 논리적인 메모리를 동일한 크기의 페이지로 잘라서 각각의 페이지별로 물리적인 메모리의 적당한 위치에 올라갈 수 있게 해주는 기법

주소 변환을 위해 **page table**이란게 사용됨.

물리적인 공간에 페이지가 들어갈 수 있는 공간을 **page frame**이라고 부름.

페이지 개수만큼 테이블의 **Entry**가 존재(0, 1, 2, 3…) 각각 테이블의 entry에는 몇번 프레임에 올라가있는지를 표시해줌

ex) n번째 페이지를 주소변환하고싶다 → 페이지 테이블에서 위에서부터 n번째 페이지를 찾아 주소변환함

> 테이블은 배열과 유사한데 각각 데이터에 대한 index를 갖고 해당 index에 바로 접근하여 조작이 가능함
> 

### Address Translation 과정

- **단계 1**: 프로세스가 논리적 주소를 생성합니다. 이 주소는 페이지 번호와 페이지 내 오프셋으로 구성됩니다.
- **단계 2**: CPU는 페이지 번호를 페이지 테이블의 인덱스로 사용합니다. 페이지 테이블은 해당 페이지 번호에 대응하는 프레임 번호를 제공합니다.
- **단계 3**: 프레임 번호가 결정되면, CPU는 이 번호와 논리적 주소의 페이지 내 오프셋을 결합하여 물리적 주소를 생성합니다. 이 물리적 주소는 실제 메모리 내의 위치를 가리킵니다.
- **단계 4**: CPU는 이 물리적 주소를 사용하여 메모리에 접근하고, 필요한 데이터를 읽거나 쓸 수 있습니다.

### Implementation of Page Table(페이지 테이블 구현)

- 페이지 테이블은 메인 메모리에 상주
- Page-Table base register (PTBR)가 페이지 테이블을 가리킴(페이지 테이블의 시작위치)
- Page-Table length register (PTLR)가 테이블 크기를 보관
- 모든 메모리 접근 연산에는 2번의 memory access 필요
- page table 접근 1번, 실제 data/instruction 접근 1번
- **속도 향상을 위해 associative register 혹은 translation look-aside buffer (TLB)라 불리는 고속의 lookup hardware cache 사용**

### TLB

**페이지 메모리에서 빈번히 참조되는 일부 엔트리를 캐시**하고 있음

메모리 상에 있는 페이지 테이블을 검색하기 전에 TLB를 먼저 검색해 봄

![Untitled](/assets/images/2024-03-09-Operating-system-12-Memory-Management--2/Untitled%201.png)

## Associative Register

Associative register(TLB) : parallel search가 가능 → TLB에는 page table중 일부만 존재

### Address translation

- page table 중 일부가 associative register에 보관되어 있음
- 만약 해당 page #가 associative register에 있는 경우 곧바로 frame #를 얻음
- 그렇지 않은 경우 main memory에 있는 page table로부터 frame #를 얻음
- TLB는 context switch 때 flush (remove old entries)

![Untitled](/assets/images/2024-03-09-Operating-system-12-Memory-Management--2/Untitled%202.png)

ε은 1보다 매우 작음(memory 접근 시간보다 작아야하니깐)

결과적으로 TLB로부터 주소 변환이 되는 비율 알파가 굉장히 높다는 것.(1에 가까움) 

## 2단계 페이지 테이블 (two-level page table)

![Untitled](/assets/images/2024-03-09-Operating-system-12-Memory-Management--2/Untitled%203.png)

cpu가 두단계의 페이지 테이블을 거쳐 주소 변환하게 되고, 그다음에 실제 메모리 접근을 하게 된다는 것

속도는 줄어들지 않지만 페이지 테이블을 사용하기 위한 공간이 줄어듦.(2단계 페이지 테이블을 사용하는 이유)

- 현대의 컴퓨터는 address space가 매우 큰 프로그램 지원
    - 32 bit address 사용시 : 2^32 (4G)의 주소 공간
        - page size가 4K시 1M개의 page table entry 필요
        - 각 page entry가 4B시 프로세스당 **4M의 page table 필요**
        - 그러나, 대부분의 프로그램은 4G의 주소 공간 중 지극히 일부분만 사용하므로 **page table 공간이 심하게 낭비됨**

**→ page table 자체를 page로 구성**

**→ 사용되지 않는 주소 공간에 대한 outer page table의 엔트리 값은 NULL (대응하는 inner page table이 없음)**

![Untitled](/assets/images/2024-03-09-Operating-system-12-Memory-Management--2/Untitled%204.png)

페이지 하나의 크기 = 4KB = 2^12이다, page table entry는 4B이다(32개의 주소 표현을 위해 32bit로 설정). 따라서 page offset은 12자리를 표현하는 12bit가 할당되는건 당연하고, 나머지 p1과 p2를 설정해야 하는데안쪽 테이블의 엔트리 개수는 1K개가 되고, 곧 2의 10승을 의미한다. 따라서 안쪽 테이블의 엔트리를 가리키는 페이지 번호가 10bit가 필요하게 되고 나머지 10bit는 바깥쪽 테이블을 가리키는 페이지 번호가 된다. **각각 10bit/10bit/12bit가 될것이다!**

64비트 시스템이고 페이지 크기는 여전히 4KB일 경우는 오프셋은 여전히 페이지 크기에 따라서 12bit일 것이다. 

하지만 64자리를 표현하기 위해서는 8B가 필요하고, 각 page table entry의 크기가 될것이다. 4kb / 8b = 1/2K이고 이는 9bit를 뜻한다

따라서 p1은 나머지 bit를 할당받아 **각각 43bit/9bit/12bit가 될것이다!**

참고하면 좋은 링크

[OS - 2.6 (MV) (6) Multi-Level Page Tables](https://velog.io/@junttang/OS-2.6-MV-6-Multi-Level-Page-Tables)