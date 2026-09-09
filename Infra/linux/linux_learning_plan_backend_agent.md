# Linux 학습 계획 — Backend Engineer를 위한 운영 기반

> 목적: Linux 자체를 암기하는 것이 아니라, **내가 만든 백엔드 서비스가 실제 컴퓨터에서 어떻게 실행되고 운영되는지 이해하고 문제를 스스로 진단할 수 있는 능력**을 만든다.
>
> 주력 진로는 Backend Engineer로 유지한다. Linux 학습은 백엔드 학습을 대체하지 않고, 백엔드 프로젝트의 실행·배포·장애 대응을 이해하기 위한 보조 축으로 운영한다.

---

## 0. 학습 철학

### 핵심 질문

Linux를 배울 때마다 다음 질문을 먼저 한다.

1. **지금 어떤 시스템을 운영하고 있는가?**
2. **현재 발생한 문제는 시스템의 어느 부분인가?**
3. **그 부분을 관찰하기 위해 어떤 개념이 필요한가?**
4. **그 개념을 확인할 수 있는 Linux 도구는 무엇인가?**
5. **관찰 → 가설 → 검증 → 조치 → 재검증의 흐름은 무엇인가?**

명령어를 먼저 외우지 않는다.

예:

```text
"CPU가 높다"
    ↓
시스템 자원 문제
    ↓
어떤 프로세스가 CPU를 소비하는가?
    ↓
process / CPU 개념
    ↓
top / ps
    ↓
프로세스 확인
    ↓
원인 가설
    ↓
검증
```

### 전체 스토리

하나의 Spring Boot 서비스를 Level 1~3에 걸쳐 계속 성장시킨다.

```text
Level 1
Spring Boot
    ↓
Linux Server 1대
    ↓
CLI / Process / Resource / Permission / 환경변수 / Basic Network

Level 2
Spring Boot + PostgreSQL + Redis
    ↓
Docker / Container
    ↓
Network / Volume / Service / Logs / Runtime

Level 3
여러 인스턴스
    ↓
CI/CD + Kubernetes + Cloud + IaC + Observability
    ↓
자동화 / Reliability / 운영
```

Linux는 이 전체 시스템의 **실행 기반**이다.

---

# 1. 학습 운영 규칙

## 시간 제한

백엔드 취업 준비가 주력이다.

- 주 2~3회
- 1회 30~60분
- 주당 약 2~3시간
- Level 1: 약 2~3주
- Level 2: 약 3~4주
- Level 3: 백엔드 프로젝트가 안정된 뒤 진행

학습량이 백엔드 학습을 침범하면 Linux 범위를 줄인다.

## 학습 비율 (가이드라인)

세션마다 초 단위로 비율을 맞추려 하지 않는다. 아래는 대략적인 무게중심이고, 실습이 밀리면 개념 설명을 줄이는 쪽으로 유연하게 조정한다.

- 개념/맥락: 짧게 (핵심 질문 1~2개로 시작)
- **실습이 세션의 절반 이상을 차지하도록 한다.**
- 회고: 미션의 무게에 맞게 (아래 "학습 기록 형식" 참고 — 모든 미션에 풀 리포트를 강제하지 않는다)

## 금지 사항

- 명령어 목록을 무작정 암기하지 않는다.
- Linux 내부 구현을 필요 이상으로 깊게 파지 않는다.
- Kubernetes YAML을 암기하는 식으로 진행하지 않는다.
- 실제 장애 상황과 무관한 도구를 학습하기 위해 시간을 쓰지 않는다.
- 백엔드 프로젝트를 중단하고 Linux만 장기간 공부하지 않는다.

---

# 2. 공통 실습 프로젝트

## Project: Backend Service Operations Lab

하나의 작은 Spring Boot 서비스를 계속 발전시킨다.

### 기본 서비스

```text
GET /health
GET /api/...
POST /api/...
```

가능하면 PostgreSQL을 사용한다.

Level 1에서는 Spring Boot 단독 실행부터 시작한다.

Level 2에서는:

```text
Spring Boot
PostgreSQL
Redis(optional)
```

를 Docker로 구성한다.

Level 3에서는 같은 서비스를 Kubernetes 환경으로 확장한다.

### 중요한 원칙

서비스 자체의 비즈니스 로직을 복잡하게 만들지 않는다.

목적은 백엔드 기능 개발이 아니라 **운영 환경을 이해하는 것**이다.

---

# 3. Level 1 — Linux를 사용하는 Backend Engineer

## Level 목표

> Linux 서버에 SSH로 접속해서 파일·프로세스·자원·권한·환경변수·기본 네트워크 상태를 확인하고, 간단한 장애를 스스로 진단할 수 있다.

### 시스템 관점

```text
User
 ↓
Shell
 ↓
Command
 ↓
Process (환경변수 포함)
 ↓
CPU / Memory / Disk
 ↓
Network
 ↓
Spring Boot
```

---

## Mission 0 — 서버에 처음 입장하라

### 상황

Linux 서버를 지급받았다.

아무것도 모르는 상태에서 서버의 현재 상태와 구조를 파악해야 한다.

### 학습 개념

- SSH
- Shell
- command
- current working directory
- absolute / relative path
- Linux filesystem 구조

### 주요 도구

```bash
ssh
pwd
ls
cd
mkdir
cp
mv
rm
find
cat
less
vi   # 또는 nano — 서버에서 설정 파일을 직접 고치려면 필요
```

> 이후 미션들에서 설정 파일(application.yml, .env 등)을 직접 열어 고치는 상황이 계속 나온다. `cat`/`less`는 읽기만 가능하므로, 여기서 최소한의 `vi`(또는 `nano`) 사용법 — 열기, 입력 모드 전환, 저장 후 종료 — 을 짚고 넘어간다.

### 성공 기준

다음 질문에 설명할 수 있다.

- SSH는 무엇인가?
- Shell은 무엇인가?
- `/`, `/home`, `/etc`, `/var`, `/tmp`는 어떤 역할인가?
- 상대 경로와 절대 경로의 차이는 무엇인가?

### 회고

가벼운 탐색 미션이므로 풀 리포트 대신 **한 줄 회고**로 충분하다: "오늘 이 서버에서 새로 안 것 한 가지는?"

---

## Mission 1 — Spring Boot 서비스를 서버에 올려라

### 상황

빌드된 `app.jar`가 있다.

Linux 서버에서 서비스를 실행해야 한다.

### 학습 개념

- 파일 이동
- Java 실행
- foreground / background
- 프로세스

### 핵심 질문

> `java -jar app.jar`를 실행하면 Linux에서는 실제로 무슨 일이 발생하는가?

### 실습

```bash
java -jar app.jar
```

그 다음 터미널을 닫았을 때 무슨 일이 일어나는지 확인한다.

### 임시 해결책 (정식 해결은 Level 2 Mission 14에서)

터미널을 닫아도 프로세스가 죽지 않게 하는 방법을 여기서 바로 하나 찾아본다.

```bash
nohup java -jar app.jar &
```

> 이건 "정식 운영 방법"이 아니라 임시방편이다. "프로그램을 그냥 실행하는 것"과 "운영 가능한 서비스로 만드는 것"의 차이는 Level 2 Mission 14(systemd)에서 다시 다룬다. 지금은 왜 `&`만으로는 부족한지(터미널 세션이 끊기면 SIGHUP을 받는다는 사실) 정도만 감을 잡으면 된다.

### 회고

한 줄 회고: "터미널을 닫으면 프로세스가 왜 죽는가? nohup은 무엇을 막아주는가?"

---

## Mission 2 — 환경변수 때문에 서비스가 다르게 동작한다

### 상황

같은 `app.jar`인데, 내 로컬에서는 잘 되고 서버에서는 DB 연결에 실패한다.

> "코드는 똑같은데 왜 환경마다 다르게 동작하는가?"

### 학습 개념

- 환경변수(environment variable)란 무엇인가
- 프로세스는 환경변수를 어떻게 상속받는가 (부모 → 자식)
- `PATH`, `JAVA_HOME` 같은 시스템 환경변수와 애플리케이션 설정용 환경변수의 차이
- Spring Boot의 `application.yml`/`application.properties`와 환경변수의 관계 (`${DB_URL}` 같은 치환)

### 도구

```bash
env
printenv
export VAR=value
unset VAR
echo $VAR
```

### 실습

1. 환경변수 없이 `app.jar`를 실행해서 실패를 재현한다.
2. `export`로 필요한 환경변수를 설정하고 다시 실행해서 정상 동작을 확인한다.
3. 새 터미널(새 셸 세션)을 열었을 때 `export`한 값이 사라지는 것을 확인한다 — 왜 사라지는가?

### 핵심 질문

> `export`로 설정한 환경변수는 왜 새 터미널에서는 보이지 않는가? "영구적으로" 설정하려면 무엇이 필요한가? (`~/.bashrc`, `~/.profile` 등을 가볍게 언급만 하고, 컨테이너에서의 정식 해결은 Level 2에서 `docker -e`/`.env` 파일로 다시 다룬다.)

### 회고

한 줄 회고: "이 서비스가 필요로 하는 환경변수는 무엇이고, 왜 필요한가?"

---

## Mission 3 — 실행 중인 프로세스를 찾아라

### 상황

API가 응답하지 않는다.

> "Spring Boot 프로세스가 실제로 실행 중인지 확인하라."

### 학습 개념

- Process
- PID
- parent / child process
- process state
- signal

### 도구

```bash
ps
ps aux
pgrep
kill
```

### 핵심 질문

> 프로그램과 프로세스는 어떻게 다른가?

---

## Mission 4 — CPU를 과도하게 사용하는 프로세스를 찾아라

### 상황

서버가 느려졌다.

미션:

> CPU 자원을 가장 많이 사용하는 프로세스를 찾아라.

### 학습 개념

- CPU utilization
- load average
- process resource usage

### 도구

```bash
top
uptime
ps aux
```

### 사고 과정

```text
서버가 느리다
 ↓
CPU 문제인가?
 ↓
현재 CPU 상태 확인
 ↓
어떤 process가 소비하는가?
 ↓
원인 가설
 ↓
검증
```

---

## Mission 5 — 메모리 부족을 진단하라

### 상황

애플리케이션이 갑자기 종료되거나 느려진다.

### 학습 개념

- RAM
- process memory
- swap
- OOM의 기본 개념

### 도구

```bash
free -h
top
ps aux
```

### 핵심 질문

> CPU와 Memory 문제를 어떻게 구별할 것인가?

---

## Mission 6 — 디스크를 복구하라

### 상황

로그가 계속 쌓여 애플리케이션이 정상적으로 동작하지 않는다.

### 학습 개념

- filesystem
- disk usage
- directory size
- log

### 도구

```bash
df -h
du -sh
du
```

### 핵심 질문

> `df`와 `du`는 각각 무엇을 측정하는가?

---

## Mission 7 — Permission denied를 해결하라

### 상황

Spring Boot가 특정 파일을 읽지 못한다.

### 학습 개념

- user
- group
- other
- read / write / execute
- ownership

### 도구

```bash
ls -l
chmod
chown
sudo
vi   # 권한 문제의 원인이 설정 파일 자체인 경우, 직접 열어 확인/수정
```

### 핵심 질문

> Linux는 왜 파일마다 접근 권한을 관리하는가?

---

## Mission 8 — 8080 포트를 찾아라

### 상황

Spring Boot가 실행 중인데 외부에서 접속할 수 없다.

### 학습 개념

```text
Process
 ↓
Socket
 ↓
Port
 ↓
Network
```

### 도구

```bash
ss -lntp
curl
```

### 핵심 질문

> "프로세스가 실행 중이다"와 "네트워크에서 접속할 수 있다"는 왜 다른가?

---

# 4. Level 1 중간 미션 — Broken Backend Server

## 상황

운영 중인 Spring Boot 서버에서 장애가 발생했다.

다음 증상 중 일부가 랜덤하게 발생하도록 구성한다.

- 프로세스가 종료됨
- CPU 과부하
- 메모리 부족
- 디스크 부족
- 잘못된 권한
- 잘못된 포트
- 환경변수 누락 (Mission 2에서 다룬 개념을 실제로 진단해본다)

## 미션

> **사용자가 다시 API를 사용할 수 있도록 서버를 복구하라.**

### 규칙

- 해결 명령어를 미리 알려주지 않는다.
- `man`, `--help` 등 로컬 문서는 사용할 수 있다.
- 모든 조치 전에 관찰 결과를 기록한다.
- 마지막에 재현/검증한다.

### Incident Report (이 미션은 풀 리포트를 작성한다)

```text
[증상]

[관찰]

[가설]

[검증 명령]

[원인]

[조치]

[재검증]

[재발 방지]
```

### Level 1 통과 기준

명령어를 외우는 것이 아니라:

> "서버가 이상하다 → 무엇부터 관찰해야 하는가?"

를 스스로 결정할 수 있어야 한다.

---

# 5. Level 2 — Linux를 이해하고 운영하는 Backend Engineer

## Level 목표

> 애플리케이션과 Linux의 관계를 이해하고, Docker/Container/Network/Volume/Service/Log를 연결해서 장애를 분석할 수 있다.

### 전체 구조

```text
Linux Host
 ├── Process
 ├── Network
 ├── Filesystem
 └── Container Runtime
        ├── Spring
        ├── PostgreSQL (+ Volume)
        └── Redis
```

---

## Mission 9 — 여러 서비스를 한 서버에서 운영하라

### 상황

Spring Boot 외에 PostgreSQL과 Redis가 필요하다.

### 문제

직접 설치해서 관리하면:

- dependency 충돌
- 환경 차이
- 설정 충돌
- 포트 관리
- 실행/종료 관리

가 발생한다.

### 질문

> "서비스의 실행 환경을 격리할 방법은 없을까?"

여기서 Container를 도입한다.

---

## Mission 10 — 컨테이너가 왜 죽는지 찾아라

### 상황

Spring container가 계속 종료된다.

### 도구

```bash
docker ps
docker logs
docker inspect
docker exec
```

### 핵심 개념

```text
Container
 ↓
Process
 ↓
Linux Kernel
```

### 반드시 이해할 것

- Container와 VM의 차이
- Container는 무엇을 격리하는가?
- Container 내부 프로세스는 무엇인가?
- 환경변수를 컨테이너에 넘기는 방법 (`docker run -e`, `--env-file`) — Mission 2에서 배운 개념이 컨테이너에서는 어떻게 표현되는가?

---

## Mission 11 — Container Resource 문제를 해결하라

### 상황

특정 container가 CPU/Memory를 과도하게 사용한다.

### 도구

```bash
docker stats
top
ps
```

### 연결

```text
Host
 ↓
Container
 ↓
Process
 ↓
CPU / Memory
```

---

## Mission 12 — Container Network 장애를 해결하라

### 상황

Spring Boot는 실행 중이다.

PostgreSQL도 실행 중이다.

그런데 Spring Boot가 PostgreSQL에 연결하지 못한다.

### 학습 개념

- IP
- Port
- Socket
- DNS
- Container network

### 도구

```bash
curl
ss
ip
dig
```

### 핵심 질문

> "localhost는 컨테이너 안에서 무엇을 의미하는가?"

---

## Mission 13 — 컨테이너를 재시작했더니 데이터가 사라졌다

### 상황

PostgreSQL container를 재시작(또는 재생성)했다.

저장했던 데이터가 전부 사라져 있다.

> "컨테이너 안의 데이터는 왜 사라지는가?"

### 학습 개념

- Container의 파일시스템은 기본적으로 컨테이너와 생명주기를 같이한다
- Volume이란 무엇인가 (host와 container 간 데이터 공유/영속화)
- Named volume vs bind mount

### 도구

```bash
docker volume ls
docker volume inspect
docker run -v ...
docker inspect   # Mounts 섹션 확인
```

### 실습

1. Volume 없이 PostgreSQL container를 띄우고 데이터를 넣은 뒤, 컨테이너를 삭제하고 다시 만들어 데이터가 사라지는 것을 재현한다.
2. Volume을 마운트한 상태로 같은 과정을 반복해서 데이터가 유지되는 것을 확인한다.

### 핵심 질문

> DB처럼 상태를 가지는(stateful) 서비스와, Spring Boot처럼 상태가 없는(stateless) 서비스는 컨테이너 운영 관점에서 무엇이 다르게 취급되어야 하는가?

---

## Mission 14 — 서비스와 로그를 운영하라

### 상황

지금까지는 `java -jar` 또는 `docker run`을 터미널에서 직접 실행해왔다. 이제 "서버가 재부팅되어도 자동으로 다시 뜨고, 죽으면 자동으로 재시작되는" 운영 가능한 서비스로 만들어야 한다.

### 학습 개념

- daemon/service
- systemd
- journal
- startup / restart policy

### 도구

```bash
systemctl
journalctl
docker run --restart=always   # 컨테이너 자체의 재시작 정책
```

### `docker logs` vs `journalctl` — 언제 무엇을 보는가

- `docker logs <container>` : 컨테이너 **내부** 프로세스(Spring Boot 등)가 stdout/stderr로 찍는 애플리케이션 로그.
- `journalctl` : **호스트에서 systemd가 관리하는 서비스**의 로그 — 예를 들어 Docker 데몬 자체, 혹은 컨테이너 없이 systemd 유닛으로 직접 등록한 프로세스, sshd 같은 시스템 서비스.

컨테이너로 도는 Spring Boot의 애플리케이션 로그는 여전히 `docker logs`로 본다. `journalctl`은 "그 컨테이너를 실행하는 Docker 자체가 정상 기동했는가", "재부팅 후 서비스가 자동으로 올라왔는가" 같은 **한 계층 위(호스트)** 를 확인할 때 쓴다는 점을 명확히 구분한다.

### 핵심 질문

> 단순히 프로그램을 실행하는 것과 운영 가능한 서비스를 만드는 것은 어떻게 다른가? (Mission 1의 `nohup &`과 비교했을 때 systemd/재시작 정책이 해결해주는 것은 무엇인가?)

---

# 6. Level 2 중간 미션 — Multi-Service Incident

## 시스템

```text
Linux Server
 ├── Spring Boot Container
 ├── PostgreSQL Container (+ Volume)
 └── Redis Container
```

## 상황

예약 API 장애 발생.

가능한 원인:

- Spring container 종료
- PostgreSQL 연결 실패
- Redis 연결 실패
- 포트 충돌
- 환경변수 오류
- container network 오류
- volume 마운트 누락/오류 (데이터 유실 또는 미인식)
- CPU/Memory 문제
- 디스크 문제
- 애플리케이션 로그 오류

## 미션

> **서비스 전체를 정상 상태로 복구하라.**

단순히 컨테이너를 재시작하는 것으로 끝내지 않는다.

반드시:

```text
증상
 ↓
범위 좁히기
 ↓
Host 상태
 ↓
Container 상태
 ↓
Process 상태
 ↓
Network 상태
 ↓
Application log
 ↓
원인
 ↓
조치
 ↓
검증
```

의 흐름으로 해결한다. (이 미션도 Section 14의 풀 리포트를 작성한다.)

---

# 7. Level 3 — DevOps / Infrastructure의 문을 연다

## Level 목표

> 서버 하나를 관리하는 것을 넘어, 여러 서비스와 서버를 자동화하고 안정적으로 운영하는 문제를 경험한다.

주의:

**이 단계부터는 선택 영역이다.**

Backend 취업 준비가 우선이며, Level 3를 전부 깊게 공부할 필요는 없다.

---

## Mission 15 — 서버를 반복해서 구성하라

### 상황

서버가 1대에서 3대로 늘어났다.

똑같은 설정(패키지 설치, 환경변수, Docker 설정)을 매번 수동으로 반복하다가 서버마다 미묘하게 설정이 달라지는(configuration drift) 문제를 겪는다.

### 질문

> "인프라도 코드로 관리할 수 없을까?"

여기서 Infrastructure as Code 개념을 배운다.

### 학습 개념

- 수동 설정의 한계 (재현 불가능성, 문서와 실제 상태의 불일치)
- 선언적(declarative) 설정 vs 명령형(imperative) 스크립트
- Idempotency(멱등성) — 같은 코드를 여러 번 실행해도 결과가 같아야 한다는 원칙

### 도구 (택 1, 가볍게)

```text
- 가장 단순한 형태: 설정을 shell script로 코드화 (idempotency는 직접 신경써야 함)
- 조금 더 정식: Ansible playbook (선언적, 멱등성 기본 제공)
```

### 실습

지금까지 서버에 수동으로 했던 설정(패키지 설치, 환경변수 파일 배치 등) 중 하나를 골라 스크립트 또는 Ansible playbook으로 옮기고, 같은 스크립트를 두 번 실행해도 안전한지(멱등한지) 확인한다.

---

## Mission 16 — Git push 한 번으로 배포하라

### 목표

```text
git push
 ↓
CI
 ↓
Test
 ↓
Build
 ↓
Docker Image
 ↓
Registry
 ↓
Deploy
```

### 핵심 개념

- CI
- CD
- Artifact
- Container Registry
- Deployment

도구 예:

- GitHub Actions

---

## Mission 17 — 서버가 여러 대가 되었다

### 상황

Spring Boot 인스턴스가 여러 개 필요하다.

```text
Node 1
 └── Spring

Node 2
 └── Spring

Node 3
 └── Spring
```

### 문제

- 배포
- 장애 복구
- 서비스 검색
- 로드밸런싱
- replica 관리

를 사람이 직접 관리하기 어렵다.

여기서 Kubernetes가 등장한다.

---

## Mission 18 — 장애가 발생해도 서비스가 살아 있어야 한다

### 학습 개념

- Pod
- Deployment
- Replica
- Service
- Health Check
- Rolling Update

핵심 질문:

> Kubernetes의 명령어나 YAML을 외우는 것이 목적이 아니라, **어떤 운영 문제를 Kubernetes가 해결하는지** 설명할 수 있는가?

---

# 8. Level 3 중간/최종 미션 — Production Incident

## 시스템

```text
Internet
    ↓
Load Balancer
    ↓
Kubernetes
 ├── Spring Pod
 ├── Spring Pod
 └── Spring Pod
        ↓
   PostgreSQL (+ Persistent Volume)
        ↓
      Redis

CI/CD
Monitoring
```

## Incident

> **[SEV-2] 예약 API 장애**
>
> 14:32부터 응답시간 증가.
> 일부 사용자는 HTTP 500을 경험한다.
> Pod 3개가 모두 실행 중인 것으로 보인다.

### 미션

원인을 직접 찾아 서비스 정상화.

가능한 원인 예:

- 특정 Pod 문제
- CPU/Memory 문제
- 애플리케이션 문제
- DB connection 문제
- Persistent Volume 문제
- Network 문제
- 잘못된 deployment
- resource limit
- health check 오류

### 최종 질문

단순 복구에서 끝내지 않는다.

```text
왜 발생했는가?
왜 사전에 발견하지 못했는가?
어떻게 자동화할 수 있는가?
어떻게 재발을 막을 것인가?
어떤 metric/log가 필요했는가?
```

까지 답한다.

---

# 9. AI Agent 운영 규칙

## Agent의 역할

이 Agent는 **Linux 강사 + Incident Commander + 학습 코치** 역할을 한다.

단순히 정답을 알려주는 챗봇이 아니다.

### 가장 중요한 역할

> 사용자가 스스로 시스템의 상태를 관찰하고 원인을 추론하도록 유도한다.

---

## Agent가 따라야 하는 학습 루프

모든 학습은 가능한 한 다음 순서를 따른다.

```text
Context
 ↓
Mission
 ↓
User hypothesis
 ↓
Investigation
 ↓
Concept explanation
 ↓
Command / Tool
 ↓
User execution
 ↓
Observation
 ↓
Next hypothesis
 ↓
Resolution
 ↓
Retrospective
```

### 원칙

사용자가 바로:

> "정답 알려줘."

라고 해도 가능한 경우 바로 정답을 주지 않는다.

먼저:

> "현재 어떤 상태인지 확인해봅시다. 어떤 정보를 먼저 확인하면 원인 범위를 줄일 수 있을까요?"

라고 질문한다.

단, 사용자가 동일한 지점에서 반복적으로 막히거나 학습 시간이 과도하게 길어지면 Hint를 제공한다.

---

# 10. Hint 단계

### Hint 1 — 방향만 제시

```text
이 문제는 애플리케이션 자체보다
운영체제 관점에서 먼저 확인하는 것이 좋습니다.
```

### Hint 2 — 개념 제시

```text
실행 중인 프로그램을 Linux는 Process라는 단위로 관리합니다.
```

### Hint 3 — 도구 제시

```text
실행 중인 Process를 확인할 수 있는 명령어를 찾아보세요.
```

### Hint 4 — 명령어 예시

```bash
ps aux
```

### Hint 5 — 실행 결과 해석

사용자가 출력 결과를 주면 함께 분석한다.

---

# 11. Agent의 설명 방식

새 개념을 설명할 때 반드시 다음 순서를 우선한다.

## ① Why

왜 이 개념이 등장했는가?

## ② Where

전체 시스템에서 어디에 위치하는가?

## ③ What

개념은 정확히 무엇인가?

## ④ How

Linux에서는 어떻게 관찰/조작하는가?

## ⑤ Example

현재 프로젝트에서는 어떻게 나타나는가?

## ⑥ Mission

직접 문제를 해결한다.

---

# 12. Agent가 피해야 할 설명

나쁜 방식:

> `ps`는 process status의 약자이며 현재 프로세스를 보여주는 명령어입니다.

좋은 방식:

> 지금 API가 응답하지 않습니다.
>
> 먼저 "Spring Boot가 실제로 살아있는가?"를 확인해야 합니다.
>
> Linux에서 실행 중인 프로그램은 Process라는 실행 단위로 관리됩니다.
>
> 따라서 먼저 Process를 관찰해야 하고, 그 도구 중 하나가 `ps`입니다.

그 다음 사용자가 직접 실행한다.

---

# 13. 모든 미션의 종료 조건

미션을 해결했다고 바로 다음으로 넘어가지 않는다. 다만 회고의 **깊이는 미션의 무게에 맞춘다.**

## 가벼운 탐색형 미션 (예: Mission 0, 3, 4, 5, 6, 8)

한두 문장짜리 **한 줄 회고**로 충분하다: "오늘 무엇을 알게 됐는가? 다음에 같은 문제를 만나면 뭐부터 볼 것인가?"

## 장애 복구형 미션 (Level 1/2 중간 미션, Level 3 최종 미션)

Agent는 최소한 다음 질문을 한다.

1. 무엇이 문제였는가?
2. 처음에 어떤 가설을 세웠는가?
3. 어떤 관찰을 했는가?
4. 왜 그 명령어를 사용했는가?
5. 관찰 결과 가설이 어떻게 바뀌었는가?
6. 실제 원인은 무엇이었는가?
7. 어떻게 해결했는가?
8. 다시 발생한다면 어디부터 확인할 것인가?

그리고 사용자가 자신의 말로 설명하도록 한다. 이 경우에만 아래 14장의 풀 기록 형식을 작성한다.

---

# 14. 학습 기록 형식 (장애 복구형 미션 전용)

장애 복구형 미션 종료 후 다음 형식으로 기록한다. 가벼운 탐색형 미션에는 이 형식을 강제하지 않는다 (13장 참고).

```markdown
# Mission N

## Situation

## System Context

## Problem

## My Hypothesis

## Investigation

### Command

### Result

### Interpretation

## Root Cause

## Resolution

## What I Learned

## If It Happens Again

## One-Sentence Explanation
```

특히 마지막:

> **One-Sentence Explanation**

은 사용자가 스스로 설명하게 한다.

---

# 15. Level별 역량 체크

## Level 1

다음 상황에서 스스로 조사할 수 있어야 한다.

- 프로세스가 죽었다
- CPU가 높다
- 메모리가 부족하다
- 디스크가 부족하다
- Permission denied
- 환경변수가 잘못되었거나 누락되었다
- 포트가 열리지 않는다

그리고:

> "서버가 이상하다."

에서 끝나지 않고 **어떤 계층을 먼저 조사할지 결정할 수 있어야 한다.**

---

## Level 2

다음 흐름을 연결할 수 있어야 한다.

```text
Application
 ↓
Process
 ↓
Container (+ Volume)
 ↓
Network
 ↓
Linux Host
 ↓
Resource
```

Docker 장애를 단순 재시작으로 해결하지 않고 원인을 조사할 수 있어야 한다.

컨테이너 재시작/재생성 시 데이터가 유지되는 것과 사라지는 것의 차이를 volume 유무로 설명할 수 있어야 한다.

---

## Level 3

다음 질문에 답할 수 있어야 한다.

- 왜 자동 배포가 필요한가?
- 왜 Container Orchestration이 필요한가?
- Kubernetes가 해결하는 문제는 무엇인가?
- 왜 Infrastructure as Code가 필요한가?
- 장애를 어떻게 감지하고 대응하는가?
- 단일 서버와 여러 서버의 운영 문제는 어떻게 다른가?

---

# 16. 최종 목표

이 학습의 최종 목표는:

> **"Linux 명령어를 많이 아는 백엔드 개발자"가 아니다.**

다음과 같은 개발자가 되는 것이다.

```text
내가 만든 Spring Boot 서비스
          ↓
실제 Linux 서버에서 실행
          ↓
Process / Resource / Network / 환경변수 이해
          ↓
Docker로 실행 환경 격리 (+ Volume으로 데이터 영속화)
          ↓
CI/CD로 배포 자동화
          ↓
Kubernetes로 확장
          ↓
Monitoring으로 관찰
          ↓
장애 발생 시
관찰 → 가설 → 검증 → 해결
```

즉,

> **내 코드가 컴퓨터 위에서 실제로 어떻게 실행되고 운영되는지를 이해하는 Backend Engineer**

가 되는 것이 최종 목표다.

---

# 17. 학습 우선순위

백엔드 취업 준비와 충돌할 경우 다음 우선순위를 따른다.

```text
1. Backend Core
   Spring / Java / Kotlin / JPA / SQL
          ↓
2. Project
   실제 서비스 구현
          ↓
3. Linux
   서비스 실행 / 관찰 / 장애 대응
          ↓
4. Docker
   실행 환경 관리
          ↓
5. Deployment / CI/CD
          ↓
6. Kubernetes
          ↓
7. IaC / SRE / Advanced Infrastructure
```

**1~3은 적극적으로 학습한다.**

**4~6은 프로젝트와 결합해서 학습한다.**

**7은 취업 이후 또는 DevOps/SRE 진로를 선택할 때 깊게 들어간다.**
