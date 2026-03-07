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

## 3. user 메시지 필터링 (주의)

`type: "user"` 라인이 모두 진짜 사람 입력은 아니다.
실제 데이터 분석 결과, **user 라인의 12-27%만 진짜 human 입력**이다.

| 종류 | 설명 | 진짜 human? |
|------|------|-------------|
| 일반 메시지 | `content`가 문자열, 사용자가 직접 입력 | O |
| `tool_result` | `content`가 배열, `type: "tool_result"` 포함 | X (자동) |
| `task-notification` | subagent 작업 알림 | X (자동) |
| `teammate-message` | 팀 에이전트 메시지 | X (자동) |
| context continuation | 컨텍스트 연속 메시지 | X (자동) |

**판별 방법:**

```python
def is_human_message(line: dict) -> bool:
    if line.get("type") != "user":
        return False
    msg = line.get("message", {})
    content = msg.get("content")
    # 문자열이면 human
    if isinstance(content, str):
        return True
    # 배열이면 tool_result 확인
    if isinstance(content, list):
        return not any(
            block.get("type") == "tool_result"
            for block in content
        )
    return False
```

**주의:** 이 포맷은 비공식이므로 Claude Code 업데이트로 변경될 수 있다.

## 4. 추출 가능한 Human Interaction 시그널

실제 세션 2개 분석 (코드 세션 45턴, 설계 세션 70턴) 에서 발견한 시그널:

| 시그널 | 설명 | 코드 세션 | 설계 세션 |
|--------|------|-----------|-----------|
| 메시지 길이 중앙값 | 인간 입력 크기 | 19자 | 41자 |
| 짧은 메시지 비율 | <20자 (승인/거부) | 51% | 37% |
| 승인 비율 | okay/yeap/yes/good | 44% | 51% |
| 거부 비율 | no/wait/nono | 7% | 4% |
| 질문 비율 | 물음표 포함 | 11% | 10% |
| 위임 깊이 | assistant턴/human턴 | 6.4 | 5.1 |
| 응답 간격 중앙값 | human 메시지 사이 시간 | 5.8분 | 4.2분 |
| 도구 다양성 | 사용된 도구 종류 수 | 16 | 7 |

**관찰된 패턴:**
- 인간은 80-90% 짧은 승인/방향 지시. 상세 스펙은 2-4%.
- **거부 순간(4-7%)이 가장 높은 시그널** — 코스를 바꾸는 결정적 피벗.
- 코드 세션은 위임 중심, 설계 세션은 협업 중심 (AskUserQuestion 13회 vs 1회).
- 1-5분 간격이 설계 세션에서 44%로 지배적 — 인간의 "숙고 모드".
