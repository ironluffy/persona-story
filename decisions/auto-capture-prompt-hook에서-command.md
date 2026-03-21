---
date: '2026-03-21'
depends_on: [DEC-001, DEC-002]
id: DEC-003
status: accepted
supersedes: []
title: Auto-capture — prompt hook에서 command hook으로
type: decision
---

# Auto-capture — prompt hook에서 command hook으로

## 원래 계획

PreCompact/Stop에 prompt hook → Claude(Haiku)가 세션 요약 → `persona log` 호출.

```json
{"type": "prompt", "prompt": "세션 요약을 persona log로 기록해주세요"}
```

## 왜 실패했나

| 가정 | 현실 | 판정 |
|------|------|------|
| PreCompact에 prompt hook | `command` 타입만 지원 | ❌ |
| prompt hook이 CLI 실행 | `{"ok": true/false}` 판단만 반환 | ❌ |
| 중복 방지를 prompt에서 처리 | DB 접근 불가 | ❌ |

prompt hook은 **판단**만 할 수 있고, **행동**은 할 수 없다.

## 전환: command hook + 전용 커맨드

```
Stop event → persona auto-capture --event stop
  → stdin에서 last_assistant_message 읽기
  → 5분 dedup 체크 (DB 쿼리)
  → DB insert (tags: ["auto-capture"])
  → exit 0
```

### 핵심 설계

- **Stop만 MVP**: `last_assistant_message` 활용 (transcript 파싱 불필요)
- **PreCompact는 Phase 2**: `transcript_path` 파싱 필요 → 복잡도 높음
- **tags 기반 구분**: `[auto]` 텍스트 접두사가 아닌 `tags: ["auto-capture"]`
- **dedup**: 5분 윈도우 + `stop_hook_active` 무한루프 방지
- **summary 추출**: 첫 비코드 라인 (200자)

### PR 리뷰에서 변경

- `[auto]` 접두사 삭제 — text에 메타데이터 넣는 것은 anti-pattern
- `matcher` 필드 추가 — hook 등록 일관성

## 교훈

- Hook 설계 전에 **반드시 이벤트별 지원 타입을 확인**할 것
- Claude Code 문서: "Events that only support `type: command`: PreCompact, SessionStart..."
- 가정 기반 설계 → 구현 시 첫 충돌 → 재설계 비용 발생
