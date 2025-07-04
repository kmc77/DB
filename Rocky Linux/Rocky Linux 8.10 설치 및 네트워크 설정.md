# Rocky Linux 8.10 설치 및 네트워크 설정

## 1. OS 설치 (Rocky 8.10)

### 가상머신 생성 및 설정
- New Virtual Machine → Rocky-8.10-x86_64-dvd1.iso 파일 삽입
- Virtual Machine name 변경: 구분할 수 있는 이름으로 설정
  - 예: `Rocky 8.10`
- 저장소 구성 확인: ISO 파일에서 설치 패키지를 제대로 불러오는지 확인

### 설치 구성
- **root 비밀번호**: `Kmc123` 입력
- **사용자 생성**: 필요시 추가 사용자 생성
- **소프트웨어 선택**: 테스트 환경에 맞춰서 선택
- **설치 목적지 설정**: 디스크 파티션 설정
- **설치 시작**

## 2. 네트워크 세팅

### GUI를 통한 네트워크 설정
1. 우측 상단 설정 → 네트워크 탭
2. 유선 연결 **off**
3. 유선 설정 → IPv4 탭
4. **수동** 선택 후 아래 사항 입력

### 호스트 컴퓨터에서 VMnet8 정보 확인
호스트 컴퓨터에서 `cmd`를 열고 `ipconfig` 명령어로 VMnet8의 IPv4 주소 확인

```cmd
ipconfig
```

**확인 결과 예시:**
```
Connection-specific DNS Suffix . :
이더넷 어댑터 VMware Network Adapter VMnet8:
   링크-로컬 IPv6 주소 . . . . : fe80::9592:ecca:7c40:97f%11
   IPv4 주소 . . . . . . . . . : 192.168.208.1 (기본 설정)
   서브넷 마스크 . . . . . . . : 255.255.255.0
   디폴트 게이트웨이 . . . . . . . :
```

## 3. 확인한 정보 바탕으로 VMware 설정 입력

### 네트워크 설정 값
- **DNS**: `168.208.63.1` (자동으로 두면 됨)
- **주소**: `192.168.208.211` 
  - 끝 부분을 3~254까지 임의 배정
  - 실행 중인 다른 가상머신과 겹치면 안됨
- **네트마스크**: `255.255.255.0`
- **게이트웨이**: `192.168.208.1`
  - 게이트웨이는 일반적으로 VMnet8의 IP 주소를 사용

## 4. 연결 테스트

### 게이트웨이 연결 확인
```bash
ping 192.168.208.1
```

**실행 결과 예시:**
```
[min@localhost ~]$ ping 192.168.208.1
PING 192.168.208.1 (192.168.208.1) 56(84) bytes of data.
64 bytes from 192.168.208.1: icmp_seq=1 ttl=128 time=0.234 ms
64 bytes from 192.168.208.1: icmp_seq=2 ttl=128 time=0.994 ms
... 생략
```

### 참고: 공용 DNS 서버
- **Google DNS**: 8.8.8.8, 8.8.4.4
- **Cloudflare DNS**: 1.1.1.1, 1.0.0.1
- **OpenDNS**: 208.67.222.222, 208.67.220.220

### 설정 완료
→ 저장(적용) → 유선 연결 **on**

---

## MobaXterm 연결

### 연결 설정 단계
1. MobaXterm 실행
2. 상단 왼쪽 `Session` 클릭
3. `SSH` 선택
4. 아래 정보 입력:
   - **Remote host**: `192.168.208.100`
   - **Specify username**: `min` (또는 root 등 실제 사용자)
   - **Port**: `22` (기본)
5. `OK` 또는 `Save & Connect`

---

## 5. VMware 간 통신을 위한 방화벽 설정

### 방화벽 상태 확인
```bash
systemctl status firewalld
```

**방화벽 켜진 상태 예시:**
```
[min@localhost ~]$ systemctl status firewalld
● firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; enabled; vendor p>
   Active: active (running) since Tue 2025-07-01 12:54:09 EDT; 4s ago
   ... 생략
```

### 방화벽 종료 및 자동 실행 차단
```bash
systemctl stop firewalld      # 방화벽 종료
systemctl disable firewalld   # 부팅 시 자동 실행 차단
```

### 방화벽 종료 상태 확인
```bash
systemctl status firewalld
```

**방화벽 꺼진 상태 예시:**
```
[min@localhost ~]$ systemctl status firewalld
● firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; disabled; vendor preset: ena>
   Active: inactive (dead)
   ... 생략
```

---

> **참고**: MobaXterm 연결 이후의 작업들은 모두 MobaXterm을 통해 진행합니다.
