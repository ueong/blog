---
date: "2026-03-01"
tags:
  - network
  - command
related:
  - "[[202603012049 네트워크 확인을 위한 명령어]]"
---

# traceroute / tracert

목적지까지 거치는 **라우터(홉)를 순서대로 나열**하여 경로와 각 구간 지연을 확인하는 도구. 패킷 손실이나 지연이 어느 구간에서 발생하는지 파악할 때 사용한다.

- Linux/macOS: `traceroute` (기본 설치 또는 패키지 설치 필요)
- Windows: `tracert` (기본 설치)

## 동작 원리

TTL을 1부터 1씩 증가시키며 패킷을 전송한다. TTL이 0이 된 라우터는 `ICMP Time Exceeded` 메시지를 발신자에게 반환하고, 그 IP가 해당 홉의 라우터로 기록된다.

## 설치 (Linux)

```bash
# Ubuntu/Debian
sudo apt install traceroute

# macOS는 기본 설치됨
```

## 기본 사용법

```bash
# Linux/macOS
traceroute <호스트>

# Windows
tracert <호스트>
```

## 주요 옵션

### Linux (`traceroute`)

| 옵션 | 설명 | 예시 |
|------|------|------|
| `-m <n>` | 최대 홉 수 (기본 30) | `traceroute -m 20 google.com` |
| `-q <n>` | 각 홉당 패킷 수 (기본 3) | `traceroute -q 1 google.com` |
| `-w <초>` | 응답 대기 시간 | `traceroute -w 2 google.com` |
| `-n` | DNS 역조회 없이 IP만 표시 | `traceroute -n google.com` |
| `-I` | ICMP 방식으로 전송 (기본은 UDP) | `traceroute -I google.com` |
| `-T` | TCP SYN 방식으로 전송 | `traceroute -T -p 443 google.com` |
| `-p <포트>` | 사용할 포트 지정 | `traceroute -T -p 80 google.com` |
| `-4` | IPv4 강제 사용 | `traceroute -4 google.com` |
| `-6` | IPv6 강제 사용 | `traceroute -6 google.com` |

### Windows (`tracert`)

| 옵션 | 설명 | 예시 |
|------|------|------|
| `-h <n>` | 최대 홉 수 (기본 30) | `tracert -h 20 google.com` |
| `-w <ms>` | 응답 대기 시간(밀리초) | `tracert -w 2000 google.com` |
| `-d` | DNS 역조회 생략, IP만 표시 | `tracert -d google.com` |
| `-4` | IPv4 강제 사용 | `tracert -4 google.com` |
| `-6` | IPv6 강제 사용 | `tracert -6 google.com` |

## 출력 읽기

```
traceroute to google.com (142.250.207.46), 30 hops max
 1  192.168.1.1       1.2 ms   1.1 ms   1.0 ms   # 게이트웨이(공유기)
 2  10.0.0.1          3.4 ms   3.2 ms   3.5 ms   # ISP 첫 번째 라우터
 3  * * *                                          # 응답 없음 (방화벽 차단)
 4  203.0.113.1      10.1 ms  10.3 ms  10.0 ms
 5  142.250.207.46   12.5 ms  12.3 ms  12.6 ms   # 목적지
```

- 3개 숫자: 동일 홉에 3번 패킷을 보낸 각각의 RTT
- `* * *`: 해당 홉이 ICMP를 차단했거나 응답하지 않음. 실제 경로는 존재할 수 있음
- 특정 구간에서 RTT가 갑자기 높아지면 그 구간에 병목이 있을 가능성이 있음
