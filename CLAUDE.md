# persona-story

persona 프로젝트의 메타 레포. 코드가 아닌 **설계, 논의, 발견, 독푸딩 기록, 백로그**를 관리한다.

## 이 레포의 역할

- 설계 결정 (design decisions)
- 스프린트 기록 (sprint logs)
- 개발 중 발견 사항 (discoveries, TIL)
- 독푸딩 피드백 (dogfooding)
- 백로그 및 아이디어 (backlog, ideas)
- 기술 리서치 노트 (research)

## 이슈 컨벤션

### 제목 접두사

이슈 제목은 반드시 `[접두사]` 로 시작한다:

| 접두사 | 용도 | 예시 |
|--------|------|------|
| `[Sprint]` | 스프린트 완료/계획 기록 | `[Sprint] S2 reflect 구현 완료` |
| `[Discovery]` | 개발 중 발견한 기술적 사실 | `[Discovery] history.jsonl은 메시지별 로그` |
| `[Design]` | 설계 논의 및 결정 | `[Design] Reflection 테이블 스키마` |
| `[Dogfooding]` | 직접 사용하며 느낀 피드백 | `[Dogfooding] reflect --review UX 개선점` |
| `[Backlog]` | 미래 구현 아이디어 | `[Backlog] 다국어 시스템 프롬프트 지원` |
| `[Bug]` | 실사용 중 발견한 버그 | `[Bug] search 한국어 fallback 누락` |
| `[Research]` | 기술 리서치 노트 | `[Research] tsvector vs pg_trgm 한국어 검색` |

### 라벨

| 라벨 | 매칭 접두사 |
|------|-------------|
| `sprint` | `[Sprint]` |
| `discovery` | `[Discovery]` |
| `design` | `[Design]` |
| `dogfooding` | `[Dogfooding]` |
| `backlog` | `[Backlog]` |
| `bug` | `[Bug]` |
| `research` | `[Research]` |

접두사에 맞는 라벨을 반드시 함께 붙인다.

토픽별 추적이 필요하면 `topic:*` 라벨을 추가한다 (예: `topic:reflect`, `topic:parser`, `topic:inbox`).

## 관련 레포

- **ironluffy/persona** — CLI 코드 레포 (코드 변경, 코드 버그는 여기)
- **ironluffy/DongminKang** — 강동민의 persona 인스턴스 데이터
- **ironluffy/persona-story** — 이 레포 (설계, 논의, 기록)

## 이슈 라우팅 가이드

- 코드 버그, 코드 변경 PR → `ironluffy/persona`
- 설계 논의, 발견 기록, 백로그, 독푸딩 → `ironluffy/persona-story` (여기)
- 개인 인스턴스 데이터, 프레임 설정 → `ironluffy/DongminKang`
