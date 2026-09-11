# Agent 공통 컨텍스트 — Linux 학습 진행 상태

> 이 문서는 **어떤 AI 에이전트(Claude, GPT 등)가 이어받아도 같은 방향·같은 규칙으로 학습을 진행**할 수 있도록 만든 핸드오프 문서입니다.
>
> 새 세션/다른 AI로 넘어올 때는 **이 문서부터 읽으세요.**

---

## 0. 참고 문서 지도

| 문서 | 역할 |
|---|---|
| [linux_learning_plan_backend_agent.md](linux_learning_plan_backend_agent.md) | 전체 학습 계획 원본 — 철학, 레벨/미션 목록, Agent 운영 규칙(9~14장) |
| [missions/README.md](missions/README.md) | 미션별 완료 여부 체크리스트 |
| [missions/level*/mission-XX-*.md](missions/) | 미션별 상세 기록 (한 일 / 관찰 / 개념 / 회고) |
| **이 문서** | 지금 당장 이어받을 때 필요한 "현재 상태" + "공통 규칙 요약" |

---

## 1. Agent가 지켜야 할 공통 규칙 (요약 — 원본은 계획서 9~14장)

1. **정답을 바로 주지 않는다.** "현재 상태를 뭐부터 확인하면 좋을까요?" 식으로 먼저 되묻고, 막히면 Hint 1(방향)→2(개념)→3(도구)→4(명령어)→5(결과 해석) 순서로 단계적으로만 힌트를 준다.
2. **미션 난이도별 회고 형식이 다르다.**
   - 가벼운 탐색형 미션(도구 익히기): 한 줄 회고로 충분
   - 장애 복구형 미션(중간/최종 미션): 계획서 13장의 8개 질문 풀 리포트 작성
3. **Mission 3부터는 "블라인드 진단" 방식이다** (사용자와 합의된 설계, 아래 4장 참고).
4. **미션 완료 시마다 반드시:**
   - `missions/level{N}/mission-XX-slug.md` 파일로 기록 작성
   - `missions/README.md` 체크리스트 갱신 (✅, 날짜, 링크)
   - 이 문서(`AGENT_CONTEXT.md`)의 "2. 현재 진행 상황" 갱신
   - `git add . && git commit -m "Mission N: ..." && git push`

### 미션 진행 형식 보정 (2026-09-11)

Mission 3부터 블라인드 진단을 유지하되, 매 미션은 사용자가 명령을 실행하기 **전** 아래 순서로 진행한다.

1. **상황과 성공 기준**: 지금 무엇이 실패했고, 어떤 관찰로 원인을 좁힐지 설명한다.
2. **개념**: Why → Where → What → How 순서로 Process·포트·메모리 등 해당 주제를 충분히 설명한다.
3. **셸 문법과 도구의 구분**: 명령어의 각 인자/옵션, 리다이렉션·파이프·백그라운드 같은 셸 문법인지 외부 프로그램인지 구분해 설명한다.
4. **실습과 해석**: 사용자가 관찰 명령을 실행하고, 출력의 의미와 다음 진단 분기까지 함께 해석한다.

정답을 먼저 말하지 않는 원칙은 유지한다. 단, 사용자가 개념 또는 명령어 설명을 요청하면 Hint 단계를 기계적으로 고수하지 말고, 요청 범위의 설명을 먼저 제공한다.

---

## 2. 실습 환경

- **서버**: AWS EC2, Ubuntu 22.04 LTS (x86), t2/t3.micro. 로컬 Mac에서 `ssh gaegosoo` 로 접속 (별칭은 로컬 `~/.ssh/config`에 등록되어 있음 — 이 저장소에는 IP/키가 없음, 보안상 의도적으로 제외).
- **개인키**: `Infra/linux/key/gaegosoo-key.pem` — `.gitignore`로 저장소에서 제외됨. 실제 파일은 로컬에만 존재.
- **비용 관리**: 세션 종료 시 EC2는 **Stop**(Terminate 아님)해서 관리. 재시작(Start) 시 퍼블릭 IP가 바뀔 수 있음 — 바뀌면 `~/.ssh/config`의 `HostName` 값 갱신 필요.
- **실습 프로젝트**: `ops-lab/` (리포 루트) — Spring Boot 최소 프로젝트, `GET /health` → `"OK"`. 빌드: `./mvnw clean package -DskipTests` → `target/ops-lab-0.0.1-SNAPSHOT.jar`. 서버의 `~/app.jar`로 배포됨.

---

## 3. 미션 설계 방식 (사용자와 합의된 규칙 — 중요)

- **Mission 0~2**: 가이드형. 개념 설명 + 순서 안내 + 같이 진행.
- **Mission 3부터 (Level 1 Mission 3~8, Level 2, Level 3 전체)**: Agent가 **서버에 미리 실제 문제 상태를 만들어두고** (프로세스 kill, CPU/메모리 점유 프로세스 실행, 디스크 채우기, 권한 변경 등 — 실제 상태이지 가짜 파일에 숨긴 퀴즈가 아님), 사용자는 명령어로 직접 진단해서 원인을 찾아냄. 정답은 미리 알려주지 않는다.
- 도구 연습이 목적인 세부 미션(find/grep 등)은 예외적으로 파일을 미리 심어두는 방식도 허용.

---

## 4. 현재 진행 상황

**마지막 갱신**: 2026-09-11 (Claude Sonnet 5 세션 → 사용자가 GPT로 이어감)

| Mission | 상태 |
|---|---|
| 0 — 서버에 처음 입장하라 | ✅ 완료 |
| 1 — Spring Boot 서비스를 서버에 올려라 | ✅ 완료 |
| 2 — 환경변수 | ✅ 완료 |
| **3 — 실행 중인 프로세스를 찾아라** | ✅ **완료** |
| **4 — CPU를 과도하게 사용하는 프로세스를 찾아라** | ✅ **완료** |
| **5 — 메모리 부족을 진단하라** | ✅ **완료** |

### Mission 3 — 완료 기록

- 시나리오: API가 응답하지 않음.
- 실제 원인: `java -jar app.jar` 프로세스가 강제 종료되어 존재하지 않았음.
- 관찰: `pgrep -fa java`가 비어 있었고, `ps`에는 현재 SSH 셸과 관찰 명령만 보였음.
- 조치 및 검증: `nohup java -jar app.jar > app.log 2>&1 &`로 재기동 후, `pgrep -fa java`에서 PID 44410과 실행 명령을 확인하고 `curl localhost:8080/health`에서 `OK`를 확인함.
- 핵심 회고: API 응답이 없으면 먼저 Java 프로세스의 실행 여부를 확인하고, 결과가 없으면 재기동한 뒤 프로세스와 health endpoint로 복구를 검증한다.

### Mission 4 — 완료 기록

- 시나리오: 서버가 느려짐.
- 실제 원인: 학습용 `yes` 프로세스(PID 44614)가 논리 CPU 코어 하나를 100% 사용 중이었음.
- 관찰: `uptime`의 load average는 `1.00, 0.78, 0.39`으로 최근 부하 증가를 보였고, `top`에서 해당 프로세스의 `%CPU`가 100.0이었음.
- 조치 및 검증: `kill 44614`로 정상 종료 신호를 보낸 뒤, `top`에서 `yes`가 사라진 것과 CPU 사용률·load average가 감소하는 것을 확인함.
- 핵심 구분: `uptime`은 CPU 사용률이 아닌 load average를 보여 주므로 `top`의 `%Cpu(s)`와 프로세스별 `%CPU`를 함께 봐야 한다. PID 종료/재시작은 즉시 영향 완화책일 수 있으나, Java 서비스 같은 실제 업무 프로세스의 높은 CPU 사용은 로그·최근 변경·스레드 상태 등을 확인해 근본 원인을 별도로 해결해야 한다.

### Mission 5 — 완료 기록

- 시나리오: 메모리 압박 때문에 애플리케이션이 느려지거나 종료될 수 있음.
- 실제 원인: 학습용 Python 프로세스(PID 44918)가 `bytearray`로 약 256MiB를 점유하고 있었음.
- 관찰: `free -h`에서 `available`이 181MiB, swap은 0B였고, `ps aux --sort=-%mem`에서 해당 프로세스가 `%MEM` 28.8, RSS 268816KiB를 사용 중임을 확인함.
- 조치 및 검증: `kill 44918` 후 해당 프로세스가 사라졌고, `available`은 420MiB로 회복됨. `curl localhost:8080/health`도 `OK`를 반환함.
- 핵심 구분: `free`가 작아도 Linux는 캐시를 회수할 수 있으므로 `available`을 중심으로 판단한다. VSZ는 가상 주소 공간, RSS는 실제 RAM 점유다. Java GC는 JVM 내부의 힙을 다루며, Linux 레벨에서는 Java를 포함한 모든 프로세스의 RSS·전체 여유 메모리·swap을 함께 관찰한다.

### 다음 예정 순서

Mission 3(진행 중) → Mission 4(CPU) → Mission 5(Memory) → Mission 6(Disk) → Mission 7(Permission) → Mission 8(Port) → **Level 1 중간 미션(Broken Backend Server, 풀 인시던트 리포트)**
