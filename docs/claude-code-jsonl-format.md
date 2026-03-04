# Claude Code JSONL 포맷

Claude Code는 대화 기록을 두 종류의 JSONL 파일로 저장한다:
1. **history.jsonl** — 전체 세션 인덱스. 사용자 메시지마다 1줄.
2. **세션 파일** — 개별 세션의 전체 대화. type 기반 구조.

이 포맷은 공식 문서화되어 있지 않으므로, 실제 데이터 기반으로 검증한 결과를 정리한다.

## 1. history.jsonl

**위치:** `~/.claude/history.jsonl`

**구조:** 세션별 1줄이 아니라, **사용자 메시지마다 1줄**. 같은 sessionId가 여러 줄에 반복된다.

```json
{"sessionId": "abc-123", "project": "/home/user/Project/persona", "display": "parser 리팩토링 해줘", "timestamp": 1709420400000}
{"sessionId": "abc-123", "project": "/home/user/Project/persona", "display": "테스트도 추가해줘", "timestamp": 1709420700000}
{"sessionId": "def-456", "project": "/home/user/Project/leva", "display": "인증 로직 수정", "timestamp": 1709421000000}
```

**필드:**

| 필드 | 설명 |
|------|------|
| `sessionId` | 세션 고유 ID |
| `project` | 프로젝트 절대 경로 |
| `display` | 사용자 메시지 텍스트 |
| `timestamp` | epoch milliseconds |

**세션 집계:** sessionId 기준으로 그룹핑하여 min/max timestamp, 첫 번째 display를 추출한다.

## 2. 세션 파일

**위치:** `~/.claude/projects/{sanitized-path}/{session-id}.jsonl`

**경로 규칙:** 프로젝트 경로의 `/`를 `-`로 치환. **선행 하이픈 유지.**

```
/home/user/Project/persona → -home-user-Project-persona
```

주의: `lstrip("-")` 하면 파일을 못 찾는다.

**type별 구조:**

| type | 용도 | 처리 |
|------|------|------|
| `user` | 사용자 메시지 | 추출 |
| `assistant` | AI 응답 | 추출 |
| `file-history-snapshot` | 파일 스냅샷 | 스킵 |
| `progress` | 진행 상태 | 스킵 |

**content 형식:** 두 가지가 있다.

```json
// 문자열
{"type": "user", "message": {"role": "user", "content": "텍스트"}}

// 배열
{"type": "assistant", "message": {"role": "assistant", "content": [
  {"type": "text", "text": "응답 텍스트"},
  {"type": "tool_use", "name": "Edit", "input": {...}}
]}}
```

content가 배열일 때 `text` 블록만 추출하고, `tool_use`/`tool_result`는 스킵한다.
