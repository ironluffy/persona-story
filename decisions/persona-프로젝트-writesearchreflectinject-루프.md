---
date: '2026-03-21'
depends_on: []
id: DEC-001
status: accepted
supersedes: []
title: persona 프로젝트 — Write→Search→Reflect→Inject 루프
type: decision
---

# persona 프로젝트 — Write→Search→Reflect→Inject

## 정의

> persona는 생각과 활동을 빠르게 기록하고(say/log), 검색하고(search), 축적된 맥락이 AI 세션에 자연스럽게 흘러들어가는(inject) Personal Knowledge CLI다.

## 핵심 가치 순서

**Write → Search → Reflect → Inject**

각 단계는 이전 단계가 충분해야 의미가 있다:
- Write가 없으면 Search할 게 없다
- Search가 안 되면 Reflect가 빈약하다
- Reflect가 없으면 Inject할 패턴이 없다

## 4-Layer Stack

| Layer | 이름 | 역할 | 상태 |
|-------|------|------|------|
| L1 | Write | say, log, reflect, auto-capture | ✅ 구현 완료 |
| L2 | Search + Reflect | search, today, week, reflect | ✅ 구현 (Pattern 미구현) |
| L3 | Frame | markdown 지식 구조화 | 미착수 |
| L4 | Seamless Use | AI가 persona를 자연스럽게 사용 | ✅ inject v1 |

## 왜 이 순서인가

- **Write first**: 마찰 없는 캡처가 모든 것의 기반. `persona say "..."` 한 줄이면 끝
- **Search**: 축적된 데이터에서 과거 맥락을 꺼내는 능력. FTS + ilike fallback
- **Reflect**: 주간 리뷰로 패턴 발견. Claude 대화 요약 → learnings/patterns/action_items
- **Inject**: AI 세션 시작 시 자동 주입. 사람이 기억을 꺼내듯 AI에게 맥락을 전달

## persona vs .md 역할 분리

- persona: 캡처 (일기) — 빠르게, 구조 없이
- .md: 정제 (위키) — 주간 리뷰 때 `persona search → md 업데이트` 흐름

## 전체 아키텍처

```
Device (Omi, Flutter App) → Infra (memyself) → Knowledge (persona) → Agent (Claude Code)
```

persona는 Knowledge 레이어. memyself와 흡수가 아닌 **연동**.
