---
date: '2026-03-21'
depends_on: [DEC-001]
id: DEC-002
status: accepted
supersedes: []
title: Inject 재설계 — 3-Pronged Approach
type: decision
---

# Inject 재설계 — 3-Pronged Approach

## 문제

inject v0은 CLAUDE.md에 inbox를 덤프하는 방식. 문제:
- CLAUDE.md가 비대해짐
- 모든 프로젝트에서 같은 내용이 주입됨
- AI가 persona를 "도구"로 인식하지 못함

## 결정

inject의 목표를 **데이터 주입 → AI의 행동 변화**로 재정의.

3가지 채널로 주입:

### 1. Skill 파일 (`~/.claude/skills/persona.md`)
- 고정 사용 가이드: `persona say`, `persona search`, `persona log`
- AI adherence ~70%
- `persona install`로 자동 생성

### 2. SessionStart hook (`inject --hook`)
- 동적 맥락을 `additionalContext`로 stdout 주입
- source 자동 감지 → 현재 프로젝트 관련 inbox만 필터링
- 100자 truncation, limit 10, "showing X of Y"
- graceful failure: 어떤 예외든 `{}` 출력 + exit 0

### 3. CLAUDE.md 마커 (`inject`)
- `<!-- persona:start -->` / `<!-- persona:end -->` 마커
- hook 없는 환경용 하위 호환

## 설계 원칙

- **source 필터링**: `.or_(repo.eq.X, repo.is.null)` — 현재 repo + 범용 항목
- **graceful failure**: hook은 절대 세션 시작을 막으면 안 됨
- **stdin 소비 + stderr 억제**: resume 시 SIGPIPE/경고 방지

## Dogfooding에서 발견한 것

- Read 커맨드(search/today/inbox)는 minimal에서도 내용 preview 필요
- limit으로 잘릴 때 전체 개수를 모름 → `get_inbox_count()` 추가
- `ensure_ascii=False` 안 하면 한국어가 unicode escape로 출력됨
