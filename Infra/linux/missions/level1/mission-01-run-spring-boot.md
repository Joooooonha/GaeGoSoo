# Mission 1 — Spring Boot 서비스를 서버에 올려라

**Level**: 1
**날짜**: 2026-09-10
**환경**: `gaegosoo` EC2, `ops-lab` (GET /health)

## 한 일

- 로컬에서 Spring Initializr로 `ops-lab` 최소 프로젝트 생성 (web 스타터, `GET /health` 컨트롤러만 추가)
- `./mvnw clean package`로 빌드 → `scp`로 서버에 jar 전송
- 포그라운드(`java -jar app.jar`)로 실행 후 터미널을 끊어서 프로세스가 같이 죽는 것 확인
- `nohup java -jar app.jar > app.log 2>&1 &`로 재실행 후 터미널을 끊어도 살아있는 것 확인

## 관찰

- 포그라운드 실행: SSH 세션 종료 → 프로세스도 함께 종료됨 (SIGHUP)
- `nohup` + `&`: SSH 세션 종료해도 프로세스 유지됨, `curl localhost:8080/health`가 계속 `OK` 반환

## One-line takeaway

터미널에 종속된 포그라운드 프로세스는 세션 종료 시 SIGHUP으로 같이 죽지만, `nohup ... &`는 SIGHUP을 무시하고 백그라운드로 분리시켜 세션과 독립적으로 살아있게 한다. 다만 이건 임시방편이고, 재부팅 시 자동 기동/재시작 정책은 아직 없음 (Level 2 Mission 14에서 systemd로 정식 해결 예정).

## 상태

✅ 통과
