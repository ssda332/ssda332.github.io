---
title: 네트워크 기기
excerpt: "study 4회차 - cs"

categories:
  - ETC

permalink: /etc/4/

toc: true
toc_sticky: true
date: 2024-03-23
last_modified_at: 2024-03-23
---

네트워크는 여러 개의 네트워크 기기를 기반으로 구축됩니다.

![Untitled](/assets/images/2024-03-23-Network-Device/Untitled.png)

> 네트워크 기기 : 전송자와 수신자가 데이터를 전달하여 단말간 통신을 가능하게 하는 장비
> 

## 네트워크 기기의 처리 범위

- 네트워크 기기는 계층별로 처리 범위를 나눌 수 있음.
- 상위 계층의 기기는 하위 계층을 처리할 수 있지만, 그 반대는 불가능함.
    - 애플리케이션 계층 : L7 스위치
    - 인터넷 계층 : 라우터, L3 스위치
    - 데이터 링크 계층 : L2 스위치, 브리지
    - 물리 계층 : NIC, 리피터, AP

## 애플리케이션 계층을 처리하는 기기

![Untitled](/assets/images/2024-03-23-Network-Device/Untitled%201.png)

![Untitled](/assets/images/2024-03-23-Network-Device/Untitled%202.png)

### L7 스위치(Load Balancing = 로드 밸런싱)

> 스위치는 여러 장비를 연결하고 데이터 통신을 중재하며 목적지가 연결된 포트로만 전기 신호를 보내 데이터를 전송하는 통신 네트워크 장비
> 

L7 스위치는 **“로드 밸런서”** 라고도 불린다.

- 서버의 부하를 분산해주는 장치 또는 기술을 뜻함
- 서버 상단 네트워크에 위치
- 서버 한대에 집중되지 않게 트래픽을 관리하여 각 서버가 최적의 효율을 발휘할 수 있게 해줌

![Untitled](/assets/images/2024-03-23-Network-Device/Untitled%203.png)

- Scale-up : 서버 자체의 성능을 확장시키는 것
- Scale-out : 여러 서버로 트래픽을 분산시켜주는 것

### 로드 밸런서 기본 기능

- **Health Check**
    - 전송 주기와 재전송 횟수 등을 설정한 이후 반복적으로 서버에 요청을 보내 정상적인 서버 또는 비정상적인 서버를 판별
- **알고리즘에 따른 분산 처리**
    - 라운드 로빈 방식 (Round Robin Method)
        - 서버로 들어온 요청을 순서대로 돌아가며 배정하는 방식.
        - 서버들이 동일한 스펙을 갖고 있고, 서버와의 연결(세션)이 오래 지속되지 않는 경우에 활용하기 적합.
    - IP 해시 방식 (IP Hash Method)
        - 클라이언트의 IP 주소를 특정 서버로 매핑하여 요청을 처리하는 방식.
        - 사용자의 IP를 *해싱(Hashing)하여 부하를 분산하기 때문에 사용자가 항상 동일한 서버로 연결되는 것을 보장.
        
        *** 해싱(Hasing) :** 임의의 길이를 지닌 데이터를 고정된 길이의 데이터로 매핑하는 것, 또는 그러한 함수.
        
        - 경로가 보장되며, 접속자 수가 많을수록 분산 및 효율이 뛰어남.
    - 최소 연결 방식 (Least Connection Method)
        - 어플리케이션 서버가 로드밸런서한테 내가 어느정도의 트래픽을 감당하고 있고, 얼마만큼의 커넥션을 맺고 있는지 알려줘서 해당 정보에 맞춰 트래픽을 할당하는 방식.
        - 가장 적은 연결(세션)상태를 보이는 서버에 우선적으로 트래픽을 할당.
        - 자주 세션이 길어지거나 서버에 분배된 트래픽들이 일정하지 않은 경우에 적합.
    - 최소 응답시간 방식 (Least Response Time Method)
        - 서버의 현재 연결 상태와 응답시간(Response Time)을 모두 고려하여, 가장 짧은 응답 시간을 보내는 서버로 트래픽을 할당하는 방식.
        - 각 서버들의 가용한 리소스와 성능, 처리중인 데이터 양 등이 상이할 경우 적합.
- **NAT (Network Address Translation)**
    - 사설 IP 주소를 공인 IP 주소로 바꾸는 데 사용하는 통신망의 주소 변조기입니다.
- **DSR (Direct Server Return)**
    - 로드 밸런서 사용 시 서버에서 클라이언트로 되돌아가는 경우 목적지 주소를 스위치의 IP 주소가 아닌 클라이언트의 IP 주소로 전달해서 네트워크 스위치를 거치지 않고 바로 클라이언트를 찾아가는 개념입니다.

### L4 로드밸런서와 L7 로드밸런서

- **L4 로드밸런서** - 4계층, 즉 네트워크 계층이나 전송 계층의 정보를 바탕으로 로드를 분산, IP 주소, 포트번호, MAC 주소, 전송 프로토콜 등에 따라 트래픽을 분산하는 것이 가능
    - 장점
        - 패킷의 내용을 확인하지 않고 로드를 분산하므로 속도가 빠르고 효율성이 높음
        - 데이터의 내용을 복호화할 필요 없기에 안전
        - 가격이 저렴
    - 단점
        - 패킷의 내용을 살펴볼 수 없으므로 섬세한 라우팅 불가
        - 사용자의 IP가 수시로 바뀌는 경우라면 연속적인 서비스를 제공하기 어려움
- **L7 로드밸런서** - 어플리케이션 계층(HTTP, SMTP, FTP)에서 로드를 분산하기 때문에 HTTP 헤더, 쿠키 등과 같은 사용자 요청을 기준으로 특정 서버에 트래픽을 분산하는 것이 가능
    - 장점
        - 상위 계층에서 로드를 분산하기 때문에 섬세한 라우팅 가능.
        - 캐싱(Cashing) 기능 제공
        - 비정상적인 트래픽을 사전에 필터링할 수 있어 서비스 안정성 높음
    - 단점
        - 가격이 비쌈
        - 패킷 내용 복호화 비용 듦
        - 클라이언트가 로드밸런서와 인증서를 공유하기 때문에, 공격자가 로드밸런서를 통해 클라이언트의 데이터에 접근할 수 있는 보안상의 위험성 존재

## 인터넷 계층을 처리하는 기기

### 라우터

- 여러 개의 네트워크를 연결, 분할, 구분 시켜주는 역할
- 패킷 소모 최소화, 경로 최적화 → 최소경로로 패킷을 포워딩하는 장비

### L3 스위치

- L3 스위치 = L2 스위치 + 라우터
- 하드웨어 기반의 라우팅을 담당하는 장비

### 게이트웨이

- 프로토콜을 서로 다른 통신망에 접속할 수 있게 해주는 장치
- LAN에서 다른 네트워크에 데이터를 보내거나, 다른 네트워크로부터 데이터를 받아들이는 출입구 역할

![Untitled](/assets/images/2024-03-23-Network-Device/Untitled%204.png)

- 공유기 - 여러 대의 컴퓨터가 하나의 인터넷 라인을 공유할 수 있도록 하는 네트워크 장비

## 데이터 링크 계층을 처리하는 기기

### 브리지

- 두 개의 근거리 통신망(LAN)을 서로 연결해주는 통신망 연결 장치

### L2 스위치

- 장치들의 MAC 주소를 MAC 주소 테이블을 통해 관리하며, 연결된 장치로부터 패킷이 왔을 때 패킷 전송을 담당한다.
- 느린 전송속도의 브리지, 허브의 단점을 개선하기 위한 장치

그 의외에도 NIC(L1, L2 포함), 스위칭 허브 등이 있음.

- NIC - 2대 이상의 컴퓨터 네트워크를 구성하는데 사용, 빠른 속도로 데이터를 송수신
- 스위칭 허브 - 여러 대의 컴퓨터를 연결하여 네트워크로 보냄. 하나의 네트워크로 수신된 정보를 여러 대의 컴퓨터로 송신

## 물리 계층을 처리하는 기기

- NIC
- 리피터
    - 들어오는 약해진 신호 정도를 증폭하여 다른 쪽으로 전달하는 장치
- AP
    - 패킷을 복사하는 기기
    - AP에 유선 LAN을 연결한 후 다른 장치에서 무선 LAN 기술(와이파이 등)을 사용하여 무선 네트워크 연결을 할 수 있음

참조

---

[로드밸런서(Load Balancer)의 개념과 특징](https://m.post.naver.com/viewer/postView.naver?volumeNo=27046347&memberNo=2521903)

[[네트워크] 로드밸런싱의 개념 및 기법 설명](https://co-no.tistory.com/entry/네트워크-로드밸런싱)

[로드 밸런싱이란? 천상계 개발자가 되려면 이 정도는 알아야지](https://www.youtube.com/watch?v=9_6COPOMZvI&t=91s)

[[네트워크 기초] 네트워크 장비](https://gaebom.tistory.com/62)