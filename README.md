# persona-story

[persona](https://github.com/Eye-of-the-Moon/persona) 프로젝트의 메타 레포.
코드가 아닌 **설계 결정, 아키텍처 논의, 발견 기록, 독푸딩 피드백, 백로그**를 관리한다.

persona는 Personal OS CLI — AI 대화, 생각, 활동을 캡처하고 분석하여 성장을 추적하는 도구.

## 아키텍처

persona는 4-Layer 아키텍처로 설계되어 있다.

```
Layer 1: Capture    — log, say, reflect로 내용 기록 (구현됨)
Layer 2: Pattern    — 도구 사용 패턴, 워크플로우 분석
Layer 3: Growth     — Frame(분석 렌즈)에 경험치 축적
Layer 4: Inject     — 학습을 Claude Code 세션에 자동 주입
```

설계 원칙:
- **벽을 치지 않는다** — 회사/개인 데이터를 분리하지 않고 source 태깅으로 출처만 기록
- **cross-context 발견이 기본값** — 회사에서 배운 것과 개인의 연결점을 자동 발견
- **모델 독립적** — 특정 AI에 종속되지 않는 지식 체계

## 관련 레포

| 레포 | 역할 |
|------|------|
| [persona](https://github.com/Eye-of-the-Moon/persona) | CLI 코드 (Python 3.13+, Click, Supabase) |
| [persona-story](https://github.com/Eye-of-the-Moon/persona-story) | 설계, 논의, 기록 (여기) |

## 로드맵

| 순서 | 이슈 | 내용 |
|------|------|------|
| 1 | [#9](https://github.com/Eye-of-the-Moon/persona-story/issues/9) Source 태깅 | git remote 자동 감지 → source_org/repo 메타데이터 |
| 2 | [#10](https://github.com/Eye-of-the-Moon/persona-story/issues/10) 모니터링 | today/week에 source 분류 + --from/--to 유연한 기간 |
| 3 | [#17](https://github.com/Eye-of-the-Moon/persona-story/issues/17) 릴리즈 | v0.1.0 태그 + 멀티 머신 설치 |
| 4 | [#22](https://github.com/Eye-of-the-Moon/persona-story/issues/22) Pattern | Claude Code tool_use 파싱 → 행동 분석 |
| 5 | [#5](https://github.com/Eye-of-the-Moon/persona-story/issues/5) Frame | markdown 기반 분석 렌즈 → reflect --frame |
| 6 | [#23](https://github.com/Eye-of-the-Moon/persona-story/issues/23) Inject | SessionStart hook → CLAUDE.md + memory/ 자동 주입 |

## Index

### Decision
- [DEC-001: persona 프로젝트 — Write→Search→Reflect→Inject 루프](decisions/persona-프로젝트-writesearchreflectinject-루프.md)
- [DEC-002: Inject 재설계 — 3-Pronged Approach](decisions/inject-재설계-3-pronged-approach.md)
- [DEC-003: Auto-capture — prompt hook에서 command hook으로](decisions/auto-capture-prompt-hook에서-command.md)

### Log
- [LOG-001: Dogfooding 발견 — UX, 데이터 모델, 실사용 패턴](log/dogfooding-발견-ux-데이터-모델-실사용-패턴.md)
- [LOG-002: persona 실사용 패턴 — 누가 어떻게 쓰는가](log/persona-실사용-패턴-누가-어떻게-쓰는가.md)

### Study
- [gcc-2026-03-23: ](docs/gcc-2026-03-23.md)
