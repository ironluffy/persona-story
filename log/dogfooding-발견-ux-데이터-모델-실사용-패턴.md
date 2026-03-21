---
date: '2026-03-21'
id: LOG-001
participants: ''
title: Dogfooding 발견 — UX, 데이터 모델, 실사용 패턴
type: log
related: [DEC-002, DEC-003]
---

# Dogfooding 발견 모음

Sprint 2~4 동안 실사용하며 발견한 것들. persona inbox에서 채굴.

## UX 발견

### minimal 모드가 너무 간소 (가장 큰 문제)

- `persona today` → `✓ Today: 2 logs, 3 thoughts` — 내용이 안 보임
- `persona search "hook"` → `✓ Found 4 result(s)` — 검색인데 결과가 안 보임
- **해결**: Read 커맨드(search/today/inbox/week)에 content preview 추가
- **원칙 발견**: Read 커맨드와 Write 커맨드의 기본 출력 수준이 달라야 한다

### inject limit 표시 부재

- `Inbox (10 pending)` — 전체 몇 개 중 10개인지 모름
- **해결**: `Inbox (showing 10 of 15)` — `get_inbox_count()` 별도 쿼리

### hook JSON 한국어 깨짐

- `json.dumps()` 기본: `\ucea1\ucc98` → 읽을 수 없음
- **해결**: `ensure_ascii=False`

### resume 시 hook 에러

- `claude --resume` → `SessionStart:resume hook error`
- **원인 추정**: stdin 미소비(SIGPIPE) + stderr 라이브러리 경고
- **해결**: stdin 읽고 버림 + stderr devnull 리디렉트

## 데이터 모델 발견

### source_repo=NULL 호환성

- inject source 필터링 시 기존 데이터(NULL)가 빠지는 문제
- `.or_(source_repo.eq.X, source_repo.is.null)` — 해당 repo + NULL 포함
- **교훈**: 새 필드 추가 시 기존 데이터의 NULL 처리를 항상 고려

### 태그 기반 메타데이터 vs 텍스트 접두사

- `[auto] 요약 내용` — text에 메타데이터 넣기 → anti-pattern
- `tags: ["auto-capture"]` — 구조적 구분이 올바름
- **교훈**: 표시 레이어에서 tag 기반으로 배지를 붙이는 것이 깨끗함

### KST 타임존 문제

- DB default `CURRENT_DATE`는 UTC 기준 → KST 자정 이후 날짜 불일치
- `datetime.astimezone()` → 로컬 타임존 자동 감지
- `_local_to_utc_range()` 헬퍼로 날짜 범위를 UTC로 변환

## 개발 프로세스 발견

### TeamCreate 병렬 에이전트

- 독립적인 파일 변경이면 2개 에이전트 동시 실행 효과적 (#37, #38 동시 완료)
- 같은 파일을 건드리면 충돌 위험

### Hook 설계 전 제약 확인 필수

- Claude Code 이벤트별 지원 타입이 다름 (PreCompact는 command만)
- prompt hook은 "판단"만, "행동"은 불가
- 가정 기반 설계 → 구현 시 첫 충돌 → 재설계 비용
