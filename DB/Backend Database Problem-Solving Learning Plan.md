# Backend Database Problem-Solving Learning Plan

## 0. 학습 목표

이 학습의 목표는 특정 DBMS의 사용법을 암기하는 것이 아니다.

최종적으로 다음 질문에 스스로 답할 수 있는 백엔드 개발자가 되는 것을 목표로 한다.

> "이 문제의 데이터 특성과 요구사항을 분석했을 때 어떤 저장소를 선택해야 하는가?"
>
> "왜 이 DB를 선택했는가?"
>
> "선택한 DB에서 어떻게 Schema, Index, Query, Transaction, Cache를 설계해야 하는가?"
>
> "트래픽과 데이터가 증가하면 어떤 문제가 발생하며 어떻게 대응해야 하는가?"

### 최종 역량

```text
요구사항
    ↓
데이터 특성 분석
    ↓
접근 패턴 분석
    ↓
정합성 / 성능 / 확장성 / 비용 판단
    ↓
DB 후보 선정
    ↓
선택 근거 작성
    ↓
Schema / Index / Query 설계
    ↓
실제 구현
    ↓
성능 측정 / 장애 발생
    ↓
원인 분석
    ↓
개선
    ↓
다른 DB를 사용했을 때와 비교
```

---

# 1. DB 라인업

모든 DB를 동일한 깊이로 학습하지 않는다.

## Tier 1 — Deep

### PostgreSQL

주력 RDBMS.

학습 범위:

- Relational Model
- Schema Design
- Normalization
- SQL
- JOIN
- Transaction
- MVCC
- Isolation Level
- Lock
- Deadlock
- Index
- B-Tree
- Composite Index
- Partial Index
- Expression Index
- GIN / GiST 개념
- EXPLAIN / EXPLAIN ANALYZE
- Query Planner
- VACUUM / Autovacuum
- PostgreSQL 특화 SQL
- JSONB
- Array
- CTE
- Window Function
- UPSERT / ON CONFLICT
- RETURNING
- PostgreSQL 운영 특성

목표:

> PostgreSQL을 단순히 "사용할 줄 아는 수준"이 아니라, 내부 동작과 선택 근거를 설명할 수 있는 수준.

---

# 2. Tier 2 — Comparative

## MySQL / MariaDB

목표는 PostgreSQL과 동일한 수준까지 공부하는 것이 아니다.

PostgreSQL을 기준으로 다른 RDBMS가 무엇이 다른지 이해한다.

비교 항목:

- Storage Engine
- InnoDB
- B-Tree Index
- Clustered Index 개념
- Transaction
- MVCC
- Isolation Level
- Lock
- Execution Plan
- JSON
- SQL Dialect
- PostgreSQL과의 차이
- Spring/JPA 환경에서의 차이

핵심 질문:

> "같은 RDB인데 PostgreSQL과 MySQL은 왜 다르게 동작하는가?"

---

# 3. Tier 2 — Redis

Redis는 "RDB 대체재"라는 관점이 아니라 별도의 역할을 이해한다.

학습 범위:

- Key-Value Model
- In-memory
- String
- Hash
- List
- Set
- Sorted Set
- TTL
- Expiration
- Cache
- Session
- Counter
- Rate Limiting
- Distributed Lock 개념
- Pub/Sub 개념
- Streams 개념
- Persistence 개념
- Eviction
- Cache Stampede 개념

핵심 질문:

> "이 데이터는 왜 PostgreSQL이 아니라 Redis에 있어야 하는가?"

---

# 4. Tier 2~3 — MongoDB

Document Database의 사고방식을 이해한다.

학습 범위:

- Document Model
- Collection
- Embedded Document
- Reference
- Flexible Schema
- Document Index
- Query
- Aggregation
- Data Modeling
- Embedding vs Referencing
- Transaction 개념
- RDB와의 모델링 차이

핵심 질문:

> "이 데이터는 관계형 모델보다 Document 모델로 표현하는 것이 자연스러운가?"

---

# 5. Tier 2~3 — Elasticsearch

Elasticsearch는 일반적인 DB 후보와 동일한 선상에 놓지 않는다.

**Search Engine**이라는 별도의 문제 영역으로 이해한다.

학습 범위:

- Inverted Index
- Full-text Search
- Tokenization
- Analyzer
- Mapping
- Query DSL
- Relevance
- Ranking
- Filtering
- Aggregation
- Index 구조
- PostgreSQL Full-text Search와 비교
- Elasticsearch를 Primary DB로 사용하지 않는 이유
- RDB + Elasticsearch 구조

핵심 질문:

> "이 문제는 왜 PostgreSQL 하나로 해결하지 않고 검색 엔진이 필요한가?"

---

# 6. 나중에 확장할 DB

처음부터 실습하지 않는다.

필요한 문제를 만나면 개념을 확장한다.

```text
Oracle
DynamoDB
Cassandra
Couchbase
Neo4j
ClickHouse
Redis 계열 확장 기술
```

목표:

> "사용할 줄 아는 것"이 아니라 "어떤 종류의 문제를 해결하기 위해 존재하는 기술인지 설명할 수 있는 것."

---

# Phase 1 — RDB 기본기를 PostgreSQL로 확립

## 목표

"SQL을 작성할 수 있다"에서

> "관계형 데이터 모델과 DB 내부 동작을 이해하고 설계할 수 있다."

로 넘어간다.

---

## Mission 1 — 데이터가 계속 중복된다

### 상황

사용자 주문 정보를 저장하는 시스템을 구현했다.

하지만 다음과 같은 데이터가 반복된다.

```text
order_id
user_name
user_email
product_name
product_price
```

사용자의 이메일이 변경되면 여러 주문 데이터를 수정해야 한다.

### 해결해야 할 문제

- 왜 중복이 발생하는가?
- 어떤 테이블로 분리해야 하는가?
- PK / FK는 어떻게 설정하는가?
- 정규화가 왜 필요한가?
- 어느 정도까지 정규화해야 하는가?

### 학습 개념

- Entity
- Relationship
- PK
- FK
- 1:N
- N:M
- Normalization
- Denormalization

---

# Mission 2 — JOIN이 필요해졌다

### 상황

주문 상세 API를 구현한다.

```text
GET /orders/{id}
```

응답에는 다음이 필요하다.

```text
주문
사용자
상품
결제
배송
```

### 해결해야 할 문제

- 어떤 JOIN이 필요한가?
- INNER JOIN과 OUTER JOIN의 차이는?
- JOIN 순서는 중요한가?
- N+1 문제는 무엇인가?
- JPA에서는 어떻게 나타나는가?

### 학습 개념

- JOIN
- Cardinality
- Execution Plan
- N+1
- Fetch Join
- Lazy / Eager Loading

---

# Mission 3 — 1천만 건에서 조회가 느려졌다

### 상황

게시글이 1,000만 개 쌓였다.

```sql
SELECT *
FROM posts
WHERE author_id = ?
ORDER BY created_at DESC
LIMIT 20;
```

응답시간이 급격히 증가한다.

### Mission

원인을 직접 조사한다.

```text
EXPLAIN
    ↓
EXPLAIN ANALYZE
    ↓
Scan 방식 확인
    ↓
Index 검토
    ↓
Index 생성
    ↓
실행 계획 비교
```

### 학습 개념

- Sequential Scan
- Index Scan
- B-Tree
- Selectivity
- Cardinality
- Composite Index
- Covering Index 개념
- Index Cost
- Write Cost

---

# Mission 4 — 인덱스를 만들었는데 느리다

### 상황

다음 인덱스를 만들었다.

```sql
CREATE INDEX idx_posts_author
ON posts(author_id);
```

하지만 여전히 느리다.

### Mission

왜 그런지 조사한다.

```text
WHERE 조건
ORDER BY
LIMIT
데이터 분포
Selectivity
Composite Index
Index Column Order
```

를 고려하여 개선한다.

### 핵심 질문

> "인덱스가 있으면 무조건 빨라지는가?"

아니오.

---

# Mission 5 — 동시 요청으로 정원이 초과됐다

### 상황

강의 정원이 30명이다.

동시에 2명의 사용자가 신청한다.

```text
A: 현재 29명 확인
B: 현재 29명 확인

A: INSERT
B: INSERT

결과: 31명
```

### Mission

동시성 문제를 해결한다.

학습 개념:

- Transaction
- Atomicity
- Isolation
- Lock
- Row Lock
- SELECT FOR UPDATE
- Race Condition
- Deadlock
- Isolation Level

---

# Mission 6 — Deadlock 발생

### 상황

두 개의 요청이 서로 다른 순서로 데이터를 수정한다.

```text
Transaction A
Lock User 1
→ User 2 대기

Transaction B
Lock User 2
→ User 1 대기
```

### Mission

- Deadlock 발생 조건을 재현한다.
- 로그를 확인한다.
- 원인을 설명한다.
- Lock 획득 순서를 통일해 해결한다.

---

# Mission 7 — 페이지네이션이 점점 느려진다

### 상황

게시물이 1억 개가 됐다.

```sql
LIMIT 20 OFFSET 90000000
```

의 성능이 나빠진다.

### 비교

```text
OFFSET Pagination
vs
Cursor Pagination
vs
Keyset Pagination
```

### 학습 개념

- OFFSET
- Cursor
- Keyset
- Composite Index
- Stable Ordering

---

# Mission 8 — PostgreSQL의 특수 기능을 활용하라

문제 상황을 통해 PostgreSQL 특화 기능을 발견한다.

예:

```text
중복 데이터 삽입 방지
→ ON CONFLICT

INSERT 후 생성된 ID 필요
→ RETURNING

JSON 구조 데이터 검색
→ JSONB

복잡한 집계
→ Window Function

특정 조건의 데이터만 빠르게 검색
→ Partial Index

특정 컬럼 조합으로 검색
→ Expression Index / Composite Index
```

목표:

> PostgreSQL 기능을 암기하지 않고 "어떤 문제에서 이 기능이 존재하는지" 이해한다.

---

# Phase 2 — DB를 선택하는 능력

## 목표

이 단계부터 PostgreSQL만 사용하지 않는다.

문제를 제시하고 여러 저장소 중 하나를 선택한다.

---

# Mission 9 — DB를 선택하라

각 문제에 대해 후보를 제시한다.

```text
PostgreSQL
MySQL
Redis
MongoDB
Elasticsearch
```

그리고 다음을 작성한다.

```text
1. 요구사항
2. 데이터 특성
3. 읽기/쓰기 패턴
4. 정합성 요구
5. 트래픽
6. 데이터 규모
7. Query 형태
8. 확장성
9. 운영 복잡도
10. 최종 선택
11. 선택하지 않은 DB의 이유
```

---

# Mission 10 — Cache가 필요하다

### 상황

상품 조회 API:

```text
100,000 req/sec
```

DB 조회 결과는 30초 정도 오래되어도 된다.

### 선택지

```text
PostgreSQL
Redis
```

### 해결

Redis Cache를 도입한다.

학습:

- Cache Aside
- TTL
- Cache Hit / Miss
- Eviction
- Cache Invalidation
- Cache Stampede
- Cache Penetration
- Cache Avalanche

---

# Mission 11 — 여러 서버가 세션을 공유해야 한다

### 상황

서버가 3대로 늘어났다.

```text
Client
  ↓
Load Balancer
  ├─ Server A
  ├─ Server B
  └─ Server C
```

Session을 각 서버의 메모리에 저장하면 문제가 발생한다.

### 해결

Redis 기반 Session 구조를 설계한다.

---

# Mission 12 — Rate Limiting

### 상황

API abuse가 발생한다.

```text
사용자당 1분에 100회
```

제한해야 한다.

### 후보

```text
PostgreSQL
Redis
```

### Mission

Redis를 활용한 Rate Limiting을 구현한다.

학습:

- Counter
- TTL
- Atomic Operation
- Race Condition

---

# Mission 13 — Schema가 계속 변화한다

### 상황

이벤트 데이터의 필드가 이벤트 종류마다 다르다.

```text
login
purchase
click
video_play
...
```

각 이벤트의 데이터 구조가 계속 추가된다.

### 후보

```text
PostgreSQL JSONB
MongoDB
```

### Mission

두 가지 모델을 모두 설계하고 비교한다.

핵심:

> "MongoDB니까 NoSQL"이 아니라 어떤 데이터 모델이 문제에 더 적합한지 판단한다.

---

# Phase 3 — 같은 문제를 다른 DB로 해결

## 목표

DB 간 차이를 체감한다.

---

# Mission 14 — 동일 기능을 3개 DB로 구현

예:

> 사용자별 최근 활동 100개 조회

PostgreSQL:

```text
Table
Index
ORDER BY
LIMIT
```

Redis:

```text
Sorted Set
```

MongoDB:

```text
Document
Index
Sort
Limit
```

비교:

```text
Data Model
Query
Index
Consistency
Performance
Scalability
Operational Complexity
```

---

# Mission 15 — PostgreSQL vs MySQL

동일한 서비스를 두 DB에 구현한다.

비교:

```text
Index
Transaction
Isolation
Lock
Execution Plan
JSON
Pagination
Upsert
SQL Dialect
```

목표:

> "둘 다 RDB인데 왜 실제 동작과 사용 경험이 다른가?"

---

# Phase 4 — Search System

# Mission 16 — LIKE 검색의 한계

### 상황

게시글 1억 건.

요구사항:

```text
"Spring Boot"
"PostgreSQL"
"Redis Cache"
```

같은 전문 검색을 제공해야 한다.

### 1차 해결

PostgreSQL:

```sql
LIKE
ILIKE
Full Text Search
```

를 사용해본다.

### 문제

검색 요구사항이 증가한다.

```text
형태소
검색어 relevance
오타
자동완성
검색 ranking
```

### 2차 해결

Elasticsearch 도입.

---

# Mission 17 — PostgreSQL + Elasticsearch

최종 구조:

```text
                 Client
                   │
                   ↓
              Spring API
                   │
          ┌────────┴────────┐
          ↓                 ↓
     PostgreSQL       Elasticsearch
       Source of          Search
         Truth            Index
```

### 핵심 학습

- Primary DB
- Search Index
- Inverted Index
- Analyzer
- Tokenizer
- Mapping
- Query DSL
- Relevance
- Eventual Consistency
- Data Synchronization

### 핵심 질문

> "왜 Elasticsearch를 Primary DB로 쓰지 않는가?"

---

# Phase 5 — Production DB 문제 해결

이 단계부터는 기술 학습보다 **장애 분석**이 중심이다.

---

# Mission 18 — DB CPU 100%

```text
Grafana
   ↓
DB CPU 100%
```

원인을 찾아야 한다.

```text
Slow Query
↓
Execution Plan
↓
Index
↓
Data Distribution
↓
Query Rewrite
```

---

# Mission 19 — DB Connection Pool 고갈

```text
Application
    ↓
Connection Pool
    ↓
DB
```

문제:

```text
Active Connection = Max
Waiting Request ↑
Latency ↑
```

조사:

- Connection Pool
- Transaction Duration
- Slow Query
- Connection Leak
- DB max_connections

---

# Mission 20 — Redis 장애

Redis가 죽었다.

질문:

```text
Cache가 없어져도 서비스가 살아야 하는가?
Session이 사라지면?
Rate Limiter가 실패하면?
Redis가 일시적으로 unavailable이면?
```

이를 통해:

- Cache 장애 대응
- Fail-open / Fail-closed
- Graceful Degradation
- Persistence
- Availability

를 학습한다.

---

# Mission 21 — Elasticsearch와 PostgreSQL 데이터가 불일치한다

```text
PostgreSQL
    ↓
상품 수정

Elasticsearch
    ↓
기존 상품 정보 검색
```

왜 발생했는지 조사한다.

학습:

- Eventual Consistency
- Dual Write
- Retry
- Outbox Pattern
- Async Processing
- Reindexing

---

# Phase 6 — Database Architecture

## 최종 종합 미션

# "서비스가 100배 성장했다"

처음:

```text
Spring Boot
     │
PostgreSQL
```

↓

```text
Spring Boot
     │
PostgreSQL
     │
Redis
```

↓

```text
              Load Balancer
                    │
        ┌───────────┼───────────┐
        ↓           ↓           ↓
    Backend A   Backend B   Backend C
        │           │           │
        └───────┬───┴───────────┘
                ↓
           PostgreSQL
                │
              Redis
                │
        Elasticsearch
```

### 종합 요구사항

```text
회원
주문
결제
상품
게시글
검색
세션
캐시
Rate Limiting
통계
```

### Mission

각 데이터를 어디에 저장할지 결정한다.

```text
회원
→ PostgreSQL

주문
→ PostgreSQL

결제
→ PostgreSQL

Session
→ Redis

Cache
→ Redis

검색
→ Elasticsearch

이벤트/비정형 데이터
→ PostgreSQL JSONB 또는 MongoDB
```

단, 이것을 정답으로 제공하지 않는다.

학습자는 **요구사항과 근거를 먼저 작성한 후 검증**한다.

---

# DB 선택 의사결정 프레임워크

모든 DB 선택 미션에서 다음 질문을 사용한다.

## 1. 데이터는 무엇인가?

```text
관계형 데이터?
Document?
Key-Value?
검색 데이터?
Time Series?
Graph?
```

## 2. 정합성이 얼마나 중요한가?

```text
매우 높음
높음
중간
낮음
```

## 3. Transaction이 필요한가?

```text
단일 작업
여러 데이터를 원자적으로 변경
분산 Transaction 필요
```

## 4. 접근 패턴은?

```text
Point Lookup
Range Query
JOIN
Aggregation
Full-text Search
Ranking
Time-based Query
Key Lookup
```

## 5. 읽기와 쓰기 비율은?

```text
Read Heavy
Write Heavy
Balanced
```

## 6. 데이터 규모는?

```text
MB
GB
TB
PB
```

## 7. 확장 방식은?

```text
Vertical Scaling
Read Replica
Sharding
Partitioning
Distributed Architecture
```

## 8. 지연시간 요구는?

```text
수 ms
수십 ms
수백 ms
수 초
```

## 9. 데이터가 유실되어도 되는가?

```text
절대 안 됨
일부 가능
Cache이므로 가능
```

## 10. 운영 복잡도는 감당할 수 있는가?

최종적으로:

```text
Requirement
    ↓
Data Model
    ↓
Access Pattern
    ↓
Consistency
    ↓
Performance
    ↓
Scale
    ↓
Operational Cost
    ↓
DB Selection
```

---

# 모든 Mission의 공통 학습 프로토콜

각 미션은 다음 순서로 진행한다.

## STEP 1 — 상황만 제시

AI Agent는 해결 방법을 먼저 알려주지 않는다.

```text
현재 시스템
요구사항
장애 상황
제약조건
```

만 제공한다.

---

## STEP 2 — 사용자가 가설을 세운다

예:

> "Index가 없어서 Sequential Scan이 발생하는 것 같다."

---

## STEP 3 — 직접 조사

사용자가:

```text
SQL
EXPLAIN
EXPLAIN ANALYZE
Docker
psql
Redis CLI
Mongo Shell
Elasticsearch API
```

등을 직접 사용한다.

---

## STEP 4 — AI가 힌트 제공

정답을 바로 제공하지 않는다.

### Hint 1

개념 방향만 제시.

### Hint 2

확인해야 할 도구 제시.

### Hint 3

확인해야 할 명령/쿼리 제시.

### Hint 4

원인 후보를 좁혀준다.

---

## STEP 5 — 해결

사용자가 직접 구현한다.

---

## STEP 6 — 검증

```text
Before
    ↓
Change
    ↓
After
```

성능/정합성/동시성 등을 직접 비교한다.

---

## STEP 7 — Self Explanation

반드시 다음 질문에 답한다.

```text
1. 무슨 문제가 발생했는가?

2. 왜 발생했는가?

3. 어떤 근거로 원인을 판단했는가?

4. 왜 이 DB/기능/구조를 선택했는가?

5. 다른 선택지는 왜 사용하지 않았는가?

6. 데이터가 100배 증가하면 어떻게 되는가?

7. 트래픽이 100배 증가하면 어떻게 되는가?

8. 장애가 발생하면 어떻게 되는가?
```

---

# ADR 기록

중요한 DB 선택은 ADR 형태로 남긴다.

```text
ADR-DB-001

Decision:
PostgreSQL을 Primary DB로 선택

Context:
주문/결제 데이터는 강한 정합성이 필요하고
사용자/상품/주문 간 관계가 복잡하다.

Alternatives:
- MySQL
- MongoDB
- Redis

Decision:
PostgreSQL

Reasons:
- ACID Transaction
- 복잡한 관계 모델
- 강력한 SQL
- PostgreSQL의 JSONB 활용 가능성
- 현재 서비스 요구사항에 적합

Trade-offs:
- 수평 확장 복잡성
- 운영 비용
- 특정 NoSQL 접근 패턴 대비 낮은 유연성
```

목표는 **"무엇을 사용했는가"보다 "왜 선택했는가"를 기록하는 것​**이다.

---

# AI Agent 역할

AI Agent는 다음 역할을 수행한다.

## 1. Database Instructor

필요한 개념을 설명한다.

단, 미션을 해결하는 데 필요한 시점에 설명한다.

---

## 2. Incident Commander

실제 장애 상황을 제시한다.

```text
DB CPU 100%
Connection Pool Exhausted
Slow Query
Deadlock
Cache Failure
Search Index Inconsistency
```

---

## 3. Reviewer

사용자의 DB 선택과 설계를 검토한다.

다음 관점에서 평가한다.

```text
Correctness
Performance
Consistency
Scalability
Maintainability
Operational Complexity
Cost
```

---

## 4. Interviewer

각 미션 종료 후 면접 질문을 한다.

예:

> "왜 PostgreSQL을 선택했습니까?"

> "Redis를 쓰지 않은 이유는?"

> "이 인덱스 컬럼 순서를 왜 이렇게 정했습니까?"

> "데이터가 100배 증가하면 어떻게 됩니까?"

> "Elasticsearch를 Primary DB로 사용하면 안 됩니까?"

---

# 학습 우선순위

현재 백엔드 취업 준비를 기준으로 한다.

```text
★★★★★
Java / Spring
Database
HTTP / API
Backend Architecture

★★★★
Testing
Docker
Linux
Monitoring

★★★
Redis
MySQL
Elasticsearch
CI/CD

★★
MongoDB
Kubernetes

★
Cassandra
DynamoDB
Oracle 심화
기타 DB
```

단, DB 내부에서는:

```text
PostgreSQL
████████████████████

DB Selection
██████████████████

Transaction / Lock
████████████████

Index / Query Optimization
██████████████████

Redis
███████████

MySQL
█████████

MongoDB
██████

Elasticsearch
████████

기타 DB
██
```

정도로 학습 시간을 배분한다.

---

# 최종 합격 기준

이 학습을 완료했다는 것은 DB 문법을 많이 외웠다는 의미가 아니다.

다음 상황에서 스스로 설계할 수 있으면 된다.

> "새로운 서비스의 요구사항이 주어졌다."

그리고:

```text
1. 데이터 특성을 분석한다.
        ↓
2. 접근 패턴을 분석한다.
        ↓
3. 정합성 요구를 판단한다.
        ↓
4. DB 후보를 선정한다.
        ↓
5. 선택 근거를 설명한다.
        ↓
6. Schema를 설계한다.
        ↓
7. Index를 설계한다.
        ↓
8. Query를 작성한다.
        ↓
9. Transaction / Lock을 설계한다.
        ↓
10. 성능을 측정한다.
        ↓
11. 병목을 분석한다.
        ↓
12. Redis / Search Engine 등의
    보조 저장소 도입 여부를 판단한다.
        ↓
13. 트래픽 증가와 장애를 고려한다.
```

최종적으로 다음과 같이 말할 수 있는 것을 목표로 한다.

> **"PostgreSQL을 많이 써봤기 때문에 PostgreSQL을 선택했습니다."**

가 아니라,

> **"이 시스템은 강한 정합성이 필요한 관계형 데이터가 핵심이고, 주문·결제·회원 간 관계가 존재하기 때문에 RDB가 적합합니다. PostgreSQL과 MySQL을 비교했을 때 현재 요구사항에서는 PostgreSQL의 X, Y 특성이 유리해 PostgreSQL을 Primary DB로 선택했습니다. 반면 고빈도 조회와 TTL이 필요한 데이터는 Redis로 분리하고, 전문 검색은 Elasticsearch로 분리하겠습니다."**

라고 설명할 수 있는 수준을 목표로 한다.