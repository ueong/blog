---
date: "2026-03-01"
tags:
  - network
  - command
related:
  - "[[202603012049 네트워크 확인을 위한 명령어]]"
---

# tcptraceroute

`traceroute`와 동일한 원리지만 UDP/ICMP 대신 **TCP SYN 패킷**으로 경로를 추적한다. ICMP/UDP를 차단한 방화벽 환경에서도 실제 TCP 통신 경로를 파악할 수 있다. Linux/macOS 전용이며 별도 설치가 필요하다.

> Windows에서는 `tracert`가 ICMP만 지원하므로, 유사한 기능은 `traceroute -T` 또는 WinMTR 등의 도구로 대체한다.

## 설치

```bash
# Ubuntu/Debian
sudo apt install tcptraceroute

# macOS (Homebrew)
brew install tcptraceroute
```

## 기본 사용법

```bash
# 루트 권한 필요
sudo tcptraceroute <호스트> <포트>

sudo tcptraceroute google.com 443
sudo tcptraceroute 8.8.8.8 53
```

## 주요 옵션

| 옵션 | 설명 | 예시 |
|------|------|------|
| `-m <n>` | 최대 홉 수 (기본 30) | `sudo tcptraceroute -m 20 google.com 443` |
| `-q <n>` | 각 홉당 패킷 수 (기본 3) | `sudo tcptraceroute -q 1 google.com 443` |
| `-w <초>` | 응답 대기 시간 | `sudo tcptraceroute -w 3 google.com 443` |
| `-n` | DNS 역조회 생략, IP만 표시 | `sudo tcptraceroute -n google.com 443` |
| `-s <포트>` | 출발지 포트 지정 | `sudo tcptraceroute -s 12345 google.com 443` |

## 출력 읽기

```
traceroute to google.com (142.250.207.46), 30 hops max, 60 byte packets
 1  192.168.1.1        1.1 ms  1.0 ms  1.1 ms
 2  10.0.0.1           3.4 ms  3.3 ms  3.5 ms
 3  * * *
 4  142.250.207.46    12.3 ms [open]  12.4 ms [open]
```

- 마지막 홉에 `[open]` 표시 → TCP 포트가 열려 있고 SYN-ACK를 받음
- `[closed]` → 도달했으나 포트가 닫혀 있음 (RST 수신)
- `* * *` → 중간 라우터가 응답 안 함 (정상일 수 있음)

## traceroute -T 와의 차이

`traceroute -T`도 TCP를 사용하지만, tcptraceroute는 좀 더 정밀한 TCP 세션 제어가 가능하고 출력이 직관적이다. 가능하면 `tcptraceroute`를 사용하는 것이 낫다.
