---
date: '2026-03-21'
id: LOG-002
participants: ''
title: persona 실사용 패턴 — 누가 어떻게 쓰는가
type: log
related: [DEC-001]
---

# persona 실사용 패턴

inbox 19개 + search 결과에서 채굴한 실제 사용 패턴들.

## 사용자 프로필

개발자 겸 기술 리더. 여러 프로젝트를 동시에 진행하면서 생각과 결정을 체계적으로 기록하고 싶어함.

## 확인된 사용 패턴

### 1. 인사이트 캡처 (say)

가장 빈번한 사용. 생각이 떠오를 때 즉시 기록:
- 리더십 자기분석, 역량 평가
- 시스템 설계 아이디어 (Shadow 엔진, node-world)
- 기술적 발견 (hook 제약, 호환성 이슈)
- 사업 관련 통찰 (노동법, PR/IR)

**핵심**: 마찰 없는 캡처. `persona say "..."` 한 줄이면 끝.

### 2. 독서 루틴 통합 (say + log + search)

> 킨들로 읽고 → 핵심은 persona say로 캡처 → 챕터 끝나면 persona log → 주간 리뷰에서 persona search로 연결

**핵심**: 읽기 → 캡처 → 검색 → 연결. 수동적 독서가 아닌 능동적 지식 축적.

### 3. 매크로 트래킹 (log)

지정학/투자 관련 주간 체크를 persona log로 기록. 별도 레포 생성 대신 persona에서 시작:
> 빌드퍼스트 편향 방지 — need가 확인되면 그때 별도 시스템

### 4. 개발 세션 기록 (log + auto-capture)

- 수동: `persona log "inject 개선 5건 구현" -t dev`
- 자동: Stop hook → `auto-capture` → 세션 종료 시 자동 기록

### 5. 프로젝트 간 맥락 이동 (inject)

여러 프로젝트 디렉토리에서 Claude Code 세션을 시작하면, source 자동 감지로 해당 프로젝트의 inbox만 주입. 다른 프로젝트의 노이즈 없이 관련 맥락만.

### 6. 의사결정 기록 (say + tags)

중요한 설계 결정을 `persona say "..." -t design`으로 기록:
- persona vs .md 역할 분리
- leva와 persona의 관계 (독립적 상호참조)
- 팀 구조 설계 (node-world 적용)

## 아직 약한 패턴

### 주간 리뷰 (reflect)
- 구현은 되어 있지만 습관화되지 않음
- `persona reflect`를 정기적으로 실행하는 루틴 필요

### 지식 구조화 (Frame — L3)
- 현재: topic 폴더에 분산 + persona 캡처
- 마이그레이션 시그널: topic 비대화, 개념 검색 어려움, 중복 발생
- 아직 시그널 미달 → study/ 폴더 신설은 보류

### inbox 처리 (process)
- 19개 쌓여 있지만 처리 안 됨
- `persona inbox --process`를 주간 리뷰에 통합해야

## 사용 빈도 (추정)

| 커맨드 | 빈도 | 맥락 |
|--------|------|------|
| `say` | 매일 2-5회 | 생각 떠오를 때 |
| `log` | 매일 1-3회 | 세션/작업 기록 |
| `search` | 주 3-5회 | 과거 맥락 검색 |
| `inject --hook` | 매 세션 (자동) | SessionStart |
| `auto-capture` | 매 세션 (자동) | Stop |
| `today/week` | 주 1-2회 | 리뷰 |
| `reflect` | 주 0-1회 | 주간 리뷰 (아직 습관화 안 됨) |
