# 1인 개발 AX 환경 설정

> 작성일: 2026-11-18 (한국 시간)
> 기준: CCGS v1.3.0 (`abc0de7`). 엔진과 무관한 공통 구성만 다룬다.
> 상세 근거: [ccgs-adoption-reference.md](ccgs-adoption-reference.md), [migration-reviews/](migration-reviews/README.md)

CCGS는 팀 규모, 리뷰 강도, 문서 깊이를 설정 키로 나눠 두고 49개 역할과 75개 스킬로 넓은 범위를 다룬다. 이 문서는 그중 **혼자 개발할 때 실제로 필요한 것만** 골라 한 곳에 모은다. 아래 내용은 원본 소스를 대조해 정리한 추천이며, 각 구성을 실제 게임에서 실행해 검증한 결과는 아니다. 예외로 §6의 helper(설정 해석, Story 필드 조회·변경, 체크포인트, 증거 stamp)는 Unreal 5.8 프로젝트에 설치한 뒤 성공 경우와 실패 경우를 모두 실행해 확인했다. Story 하나를 끝까지 진행하는 확인(§10의 3단계)은 아직 하지 않았다.

---

## 1. 원칙

1. **설정 단계를 줄여도 검증 단계는 줄이지 않는다.** 1인 모드에서 줄어드는 것은 사전 문서와 승인 절차다. 실제로 실행해 보는 일과 증거를 남기는 일은 그대로 둔다.
2. **고정할 값은 설정 키로 두지 않는다.** 1인 개발에서는 바뀔 일이 없는 값을 매번 해석하는 비용이 효과보다 크다. 실제로 바꿔 가며 쓰는 값은 `automation` 하나뿐이다.
3. **파일은 작성자와 소비자를 함께 가져온다.** 스킬 하나만 복사하면 그 스킬이 부르는 helper, 템플릿, 참조 문서가 빠져 첫 단계에서 실패한다.

---

## 2. 고정 동작값

원본의 `modes.rigor: minimal`은 여섯 하위 설정을 아래 값으로 펼친다. 1인 개발에서는 이 값을 그대로 고정한다.

| 원본 설정 | 고정값 | 실제로 일어나는 일 | 팀 설정에서 빠지는 것 |
|---|---|---|---|
| `modes.workflow` | `minimal` | 코드 작성 전에 필요한 것은 한 장짜리 `design/game-brief.md`와 엔진 설정뿐이다. brief의 Build order가 곧 계획이므로 스프린트 계획이 따로 없다. GDD, 아키텍처 문서, ADR은 필요할 때 자발적으로 쓴다 | 시스템별 8절 GDD, 전체 아키텍처, 아트 바이블, UX 명세 필수 요구 |
| `docs.density` | `terse` | 각 절을 짧게 쓴다 | 절마다 긴 설명 |
| `qa.level` | `minimal` | Logic·Integration의 자동 테스트 파일은 필수가 아니다. **UI·Visual 변경의 보관 스크린샷은 면제되지 않는다** | 모든 로직의 단위 테스트 필수 요구 |
| `modes.story_granularity` | `coarse` | Story를 크게 나눈다. 작업 하나가 기능 하나에 가깝다 | 잘게 쪼갠 Story와 스프린트 단위 배분 |
| `modes.review_mode` | `solo` | director gate를 전부 건너뛴다. Story 종료 기록에는 `Reviewed by: self, no independent review`가 남는다 | 감독·리드의 단계별 승인 |
| `team.size` | `individual` | `team-*`를 실행하면 최소 역할만 활성화된다 | 부서 단위 역할 묶음 동시 호출 |

**주의:** `review_mode: solo`와 `team.size: individual`이 **모든 서브에이전트를 끈다는 뜻은 아니다.** `dev-story`는 이 두 값을 읽지 않는다. 구현은 프로그래머 역할에, 엔진 검토는 엔진 전문 역할에 계속 위임한다(§6.2).

### 2.1 남겨 둘 조절 항목: `modes.automation`

| 값 | 동작 | 1인 개발에서 쓰는 때 |
|---|---|---|
| `collaborative` (기본) | 모든 결정과 쓰기를 묻는다 | 평소 작업 |
| `guided` | 큰 결정만 묻고 작은 결정은 알린 뒤 진행한다 | 방향이 정해진 반복 작업 |
| `autonomous` | 결정을 기록하며 진행한다. 범위 변경·파일 삭제·스키마 변경은 그래도 묻는다 | 자리를 비우는 동안의 일괄 작업. `project.local.yaml`로 잠시 켜고 끝나면 되돌린다 |

autonomous의 결정 기록은 `production/session-logs/decision-log.md`에 남는다. 돌아와서 이 파일을 확인하는 단계를 빼먹지 않는다.

### 2.2 고정하는 두 가지 방법

| 방법 | 하는 일 | 장점 | 비용 |
|---|---|---|---|
| A. 설정에 맡기기 | `project.yaml`에 `modes.rigor: minimal`만 두고 하위 여섯 키는 쓰지 않는다 | 원본 스킬을 수정하지 않는다 | `yaml-helper.sh`와 설정 해석 문서 3개(config-resolution, automation-modes, workflow-modes)를 계속 유지한다 |
| B. 스킬에 새기기 | 각 스킬에서 모드 분기를 지우고 minimal 동작만 남긴다 | 스킬이 짧아지고 설정 해석 단계가 사라진다 | 스킬마다 분기를 직접 고쳐야 하고, 원본 업데이트를 합치기 어려워진다 |

처음 도입할 때는 A가 안전하다. 실제로 쓰는 스킬이 몇 개로 굳어진 뒤 B로 옮긴다. B로 옮길 때 `automation` 분기와 실행 검증 단계는 지우지 않는다.

---

## 3. `project.yaml`

```yaml
schema_version: 1

project:
  name: "<게임 이름>"
  stage: "Concept"          # 진행에 따라 갱신
  goal: "hobby"             # jam | learn | hobby | portfolio | sell | early_access | publisher | mobile | other

modes:
  rigor: "minimal"          # 방법 B로 옮겼다면 이 줄을 삭제
  automation: "collaborative"

engine:
  name: "<Web | Godot | Unity | Unreal>"
  version: "<설치된 버전>"
  language: "<언어>"
  # code_root / test_root: engines.tsv의 기본 경로와 다를 때만 적는다
  # Unreal은 engines.tsv의 test_root가 `Source/<Module>/Private/Tests`로 자리표시째 해석된다.
  # 테스트 폴더를 만든 뒤 test_root: "Source/<모듈>/Private/Tests"를 적는다(없는 폴더는 invalid)

commands:                   # 실제로 실행되는 명령. 스킬이 빌드·실행 관찰에 사용한다
  build: "<빌드 명령>"
  test:  "<테스트 명령>"
  run:   "<창 모드 실행 명령>"    # dev-story가 화면 관찰에 사용
  smoke: "<최소 기동 확인 명령>"
```

- **쓰지 않는 키:** `modes.workflow`, `modes.review_mode`, `docs.density`, `qa.level`, `modes.story_granularity`, `team.size`. 하나라도 쓰면 그 값이 고정되어 rigor 전개를 가린다.
- **`project.local.yaml`(gitignore):** `automation`을 잠깐 바꿀 때만 쓴다. 원본의 로컬 재정의 허용 목록과 두 개발자 충돌 규칙은 팀 협업용이므로 따로 신경 쓰지 않아도 된다.
- **`commands.*`:** 명령이 적혀 있다는 사실과 그 명령이 성공한다는 사실은 별개다. 처음 한 번은 직접 실행해 결과를 확인한다.

---

## 4. `CLAUDE.md`

매 세션 컨텍스트에 실리므로 짧게 유지한다. 넣을 것은 다음 다섯 가지다.

1. 엔진, 언어, 코드 루트, 테스트 루트
2. 협업 규칙: 쓰기 전에 확인한다, 커밋은 요청이 있을 때만 한다
3. 세션 체크포인트 위치: `production/session-state/active.md`. 읽기는 자동이고 갱신은 `/checkpoint`를 실행할 때만 한다. 핵심 경로(§6.1)를 들인 뒤에는 `dev-story`·`story-done`이 Story 작업을 마칠 때도 쓰므로 이 문장을 함께 고친다. "매 세션 진행 상황을 갱신하라" 같은 지시는 넣지 않는다(§7)
4. 검증 원칙(§8)의 요약 세 줄
5. 상세 문서는 필요할 때 읽는다는 안내. `@` import는 매 세션 꼭 필요한 짧은 문서에만 쓴다
6. (핵심 경로를 들인 뒤) 작업 흐름 한 단락: brief(`/brief-from-doc` 또는 `/brainstorm`) → `/create-stories` → `/dev-story` → `/story-done`. 기획서가 따로 있으면 brief는 기획서에서 뽑은 한 장이고 기획서를 대신하지 않으며, 기획서를 고친 뒤에는 `/brief-from-doc`을 다시 실행한다고 적는다

원본에서 빼는 것:

| 원본 항목 | 이유 |
|---|---|
| `coordination-rules.md`의 수직 위임·수평 협의·충돌 상향 규칙 | 감독·리드·전문가 3단 조직을 전제로 한다 |
| Subagents vs Agent Teams 설명 | Agent Teams는 여러 세션을 엮는 실험 기능이다 |
| 모델 티어 배정 표 | 역할이 몇 개뿐이면 관리할 대상이 없다 |

원본에서 남길 만한 것: **설계 문서는 절 단위로 읽는다**(필요한 heading만 Grep → offset·limit으로 Read), **엔진·플러그인 저장소에 이슈나 PR을 직접 올리지 않는다.**

---

## 5. `.claude/settings.json`

원본 `settings.json`을 통째로 덮어쓰지 않는다. 아래 조각만 기존 설정에 병합한다.

### 5.1 권한

| 구분 | 내용 |
|---|---|
| `deny` (그대로 유지) | `rm -rf` 계열, `git push --force` 계열, `git reset --hard`, `git clean -f` 계열, `sudo`, `chmod 777`, `.env` 읽기·편집, PowerShell의 같은 명령 |
| `deny` (엔진에 맞게 추가) | 텍스트 도구로 편집하면 깨지는 엔진 바이너리 에셋 확장자 |
| `allow` | **실제로 쓰는 helper만** 추가한다. §6.4의 스크립트 중 설치한 것만 등록한다 |

### 5.2 훅

| 훅 | 이벤트 | 1인 판단 | 이유 |
|---|---|---|---|
| `session-start.sh` (축소) | SessionStart | **필수, 읽기 전용** | 브랜치와 CHECKPOINT를 대화에 주입한다. active.md를 쓰지 않는다. 업데이트 확인과 버그 집계는 떼어 낸다. 실행 시점은 §5.3의 matcher로 좁힌다 |
| `session-stop.sh` | Stop (async) | 제외 권장 | **응답마다** 실행되어, active.md가 바뀌었으면 사본을 `session-logs/`에 덧붙이고 Git 활동을 기록한다. 기록을 직접 관리한다면 필요 없다 |
| `pre-compact.sh` | PreCompact | 제외 | 압축 시각을 한 줄 기록할 뿐이다. 요약을 저장하는 기능이 아니다 |
| `notify.sh` | Notification | 선택 | 권한 요청과 대기 상태를 Windows 알림으로 알린다 |
| `validate-read.sh` | PreToolUse Read | 조건부 | 문서가 40KB를 넘기 시작하면 켠다 |
| `detect-gaps.sh` | SessionStart | 제외 | 처음 시작하는 사용자를 위한 안내다 |
| `validate-commit.sh`, `validate-push.sh` | PreToolUse Bash | 제외 | 보호 브랜치와 커밋 형식은 팀 운영 규칙이다. 1인은 커밋 시점에 직접 확인한다 |
| `validate-write.sh`, `validate-registry.sh` | PreToolUse Write | 제외 | 원본 경로와 중앙 레지스트리를 전제로 한다 |
| `validate-assets.sh`, `validate-skill-change.sh` | PostToolUse | 제외 | 이미 쓴 뒤에 실행되는 경고다. 스킬을 자주 고치기 시작하면 재검토한다 |
| `log-agent.sh`, `log-agent-stop.sh`, `validate-agent-artifact.sh` | Subagent* | 제외 | 여러 역할의 호출을 감사하는 장치다 |

세 세션 훅 중 어느 것도 체크포인트 내용을 쓰지 않는다. 원본에서 active.md를 쓰는 것은 `dev-story`와 `story-done`뿐이다. 따라서 Stop과 PreCompact를 빼고 기록 시점을 §7의 방식으로 정하면, 질문만 하는 세션에서는 아무것도 기록되지 않는다.

원본의 `features.session_state: off`는 이 목적에 맞지 않는다. 이 값은 시작 시 주입까지 모든 세션 훅을 끄지만, `dev-story`와 `story-done`의 기록은 막지 않는다.

### 5.3 최소 조각

```json
{
  "hooks": {
    "SessionStart": [
      { "matcher": "resume|compact", "hooks": [
        { "type": "command",
          "command": "echo `# <#` >/dev/null; bash \"$CLAUDE_PROJECT_DIR/.claude/hooks/session-start.sh\"; exit $? #> ; echo 'Git Bash 없음: 세션 복원 훅이 꺼져 있다'; exit 0",
          "timeout": 20 }
      ]}
    ]
  }
}
```

SessionStart의 `matcher`는 세션이 시작된 방식(`startup`, `resume`, `clear`, `compact`)으로 걸러진다.

| matcher | 체크포인트가 주입되는 때 | 맞는 사용 방식 |
|---|---|---|
| `resume\|compact` (추천) | 이전 대화를 이어 열 때, 대화가 압축된 직후 | 새로 연 세션은 질문용으로 깨끗하게 둔다. 새 세션에서 작업을 이어 가려면 `/checkpoint show`로 직접 불러온다 |
| `resume\|compact\|clear` | 위 경우에 더해 `/clear` 직후 | `/clear`를 주로 같은 작업을 정리할 때 쓰는 경우 |
| `""` (원본) | 모든 시작 | 대부분의 세션이 작업을 이어 가는 세션인 경우 |

compact는 자동으로 일어나므로 어떤 방식이든 matcher에 남긴다.

Windows에서는 훅이 Git Bash로 실행된다. Git Bash가 없으면 훅이 조용히 건너뛰어지므로 위처럼 꺼졌다는 메시지를 남기고, 첫 세션에서 체크포인트가 실제로 출력되는지 확인한다. `statusLine`과 `worktree` 설정은 취향에 따라 선택한다.

---

## 6. 최소 구성 묶음

### 6.1 스킬

핵심 경로는 **brief → create-stories → dev-story ↔ story-done** 네 단계다.

| 단계 | 스킬 | 가져올 파일 | 비고 |
|---|---|---|---|
| 기획 입력 (기획서 있음) | **`brief-from-doc`** (로컬 추가) | `SKILL.md`, `templates/game-brief.md` | 직접 쓴 기획서에서 brief 한 장을 뽑는다. 새 컨셉을 묻지 않는다. 기획서를 고친 뒤 다시 실행하면 추가 후보만 제안한다(§6.1.1) |
| 기획 입력 (아이디어만 있음) | `brainstorm` (원본 그대로) | `SKILL.md`, `templates/game-brief.md` | 컨셉 후보 2~3개를 고르는 질문부터 시작해 brief를 대화로 채운다 |
| 작업 분할 | `create-stories` | `SKILL.md`, `CONTRACT.md` | `EPIC.md`와 Stories 표를 스스로 만든다. `create-epics`가 따로 필요하지 않다 |
| 구현 | `dev-story` | `SKILL.md`, `CONTRACT.md`, `references/engine-checks.md`, `references/version-mismatch.md` | 프로그래머 위임 → 엔진 검토 → 빌드 → 실행·스크린샷 → 체크포인트 갱신 |
| 종료 | `story-done` | `SKILL.md`, `CONTRACT.md`, `references/tech-debt-row.md` | 완료 판정, Status 갱신, 다음 Story 안내. `review-gates.md`는 gate를 실행할 때만, `sprint-next.md`는 스프린트 계획이 있을 때만 읽으므로 1인 구성에서는 빼도 된다 |

#### 6.1.1 기획서가 이미 있을 때: `/brief-from-doc`

원본에서 brief를 쓰는 스킬은 셋인데, 직접 쓴 기획서를 입력으로 받는 것은 없다.

| 원본 스킬 | 기획서가 있을 때 맞지 않는 이유 |
|---|---|
| `brainstorm` | 1단계가 매번 컨셉 후보 2~3개를 고르게 한다("every run asks"). 정해진 컨셉을 건너뛰는 분기가 없다. 또 템플릿 규칙대로 `In your words`에 긴 문서를 통째로 넣으면 기획서와 같은 내용이 두 곳에 생긴다 |
| `prototype` | brief를 쓴 뒤 플레이 가능한 시제품까지 만드는 것이 본업이다 |
| `reverse-document brief` | 이미 있는 코드나 시제품에서 거꾸로 쓴다. 코드가 없으면 쓸 수 없다 |

그래서 brainstorm은 원본 그대로 두고 별도 스킬을 만들었다.
위치: [.claude/skills/brief-from-doc/SKILL.md](../.claude/skills/brief-from-doc/SKILL.md). checkpoint처럼 로컬에서 추가한 스킬이라 `studio-tests/catalog.yaml`과 README의 스킬 수에는 넣지 않았다.

| 항목 | 동작 |
|---|---|
| 실행 | `/brief-from-doc <기획서 경로>`. 경로가 없으면 `Doc/`, `docs/`, `design/`에서 후보를 찾아 묻는다 |
| 원본 관계 | 기획서가 원본이고 brief는 요약이다. `In your words`에는 기획서를 복사하지 않고 `Source: <경로> · git <해시> · <날짜>` 한 줄만 둔다. 기획서는 어떤 경우에도 쓰지 않는다 |
| 형식 | 템플릿의 영어 heading을 그대로 쓴다(`/create-stories`, `/story-done`, `/cut`이 이 이름으로 읽는다). 제목은 영문이고 기본값은 `project.name`이다. `/create-stories`가 제목으로 epic 폴더 이름을 만든다 |
| Features | 7개 이하. 각 줄에 기획서의 수치·조건·출처 절을 넣는다. `/create-stories`는 brief만 읽으므로, 줄에 규칙이 없으면 인수 기준을 지어내게 된다 |
| 출처 표시 | 기획서에 그대로 있으면 표시 없음, 이끌어 냈으면 `*(inferred (strong))*`, 없는데 필수 필드라 제안했으면 `*(guessed)*`. 선택 필드는 기획서에 없으면 비운다. 초안 아래에 필드별 출처 표, guessed 필드, 비운 필드를 붙인다 |
| `Your systems` | 비워 둔다. 채우면 `/create-stories`가 thin/deepen Story와 `/design-system`을 요구하는 경로로 바뀐다 |
| 다시 실행 | 해시가 같으면 `UNCHANGED`로 끝난다. 바뀌었으면 추가·수정 후보만 before → after로 보여준다. `[x]`, `cut`, `Keep`, 사용자가 직접 고친 줄은 건드리지 않는다 |
| 쓰기 | 초안과 출처 표를 보여준 뒤 허락을 받고 쓴다. 끝은 `COMPLETE`, `UNCHANGED`, `NOT WRITTEN — <이유>` 가운데 하나다 |

확인한 것: `git hash-object`가 기획서 한 글자 변경도 감지하고, 없는 파일에는 exit 128로 실패한다. 스킬 전체를 실제 기획서로 실행해 본 확인은 아직 없다. `skill-lint.sh`의 check4는 영어 문구("May I write" 등)만 세므로, 한국어로 쓰기 전에 묻는 이 스킬과 checkpoint는 0으로 나온다.

필요해지면 하나씩 추가한다.

| 스킬 | 추가할 때 |
|---|---|
| `quick-design` | 동작이 이미 정해진 작은 변경을 짧게 명세할 때 |
| `design-system` | 동시성, 판정 규칙처럼 추측하면 버그가 되는 기능을 상세화할 때 |
| `architecture-decision` | 되돌리기 비싼 기술 선택이 생겼을 때. ADR 한 장만 쓰면 된다 |
| `code-review` | 구현자와 다른 시각의 읽기 전용 검토가 필요할 때 |
| `smoke-check` | 빌드를 다른 사람에게 넘기기 전 |
| `story-readiness` | Story가 모호해서 구현 중에 자꾸 멈출 때 |
| `bug-report`, `playtest-report` | 플레이 가능한 빌드가 생긴 뒤 |
| `cut`, `scope-check` | 기능이 쌓여 마무리 범위를 정해야 할 때 |
| `next-step` | 다음에 할 일이 막연할 때 |

### 6.2 에이전트

`dev-story`는 Story 종류에 따라 프로그래머 한 명에게 구현을 맡기고, 엔진 전문 역할에게 읽기 전용 검토를 맡긴다. 따라서 **게임에 실제로 있는 Story 종류의 프로그래머 + 엔진 전문 역할 한 명**이면 충분하다.

| Story 종류 | 구현 역할 | 필요한 게임 |
|---|---|---|
| 일반 게임플레이, Visual/Feel | `gameplay-programmer` | 모든 게임 |
| Foundation(코어 시스템) | `engine-programmer` | 모든 게임 |
| UI | `ui-programmer` | HUD·메뉴가 있는 게임 |
| 네트워크 | `network-programmer` | 멀티플레이 게임 |
| AI·경로 탐색 | `ai-programmer` | AI가 있는 게임 |
| 엔진 검토(읽기 전용) | `<엔진>-specialist` 한 명 | 모든 게임. `code-review`도 이 역할 하나만 부른다 |
| Config/Data | 없음 | 위임 없이 직접 처리한다 |

필요 없는 역할: creative/technical/art/audio/narrative director, producer, 각 lead, qa-lead, release-manager, community-manager, live-ops-designer, analytics-engineer. solo 모드는 이들이 맡는 gate를 모두 건너뛴다. 기획을 상세히 쓸 때 `game-designer`, `systems-designer`를 추가하는 정도면 된다.

위임할 때는 입력(읽을 파일·절), 쓰기 범위, 산출물 경로, 판정의 뜻(PASS·FAIL·NOT ASSESSED)을 명시한다. 같은 파일을 두 역할이 동시에 고치게 하지 않는다.

역할 파일의 "Reports to"·위임 대상에는 들이지 않은 역할(lead-programmer, technical-director, Unreal의 `ue-*` 하위 전문가 등)이 그대로 남는다. `dev-story`에서 엔진 역할은 읽기 전용 검토만 하므로 영향은 작지만, 해당 영역(복제·UMG·GAS) 작업이 잦아지면 그 하위 전문가를 추가한다.

### 6.3 규칙

규칙은 경로가 맞는 파일을 **읽을 때만** 로드된다. 새 파일만 쓰는 경우에는 규칙이 로드되지 않을 수 있으므로, 쓰기 스킬에서 해당 규칙을 직접 읽도록 연결한다. 구체적으로는 `dev-story/SKILL.md` Phase 5의 **Sources** 목록에 "작성 전에 읽을 규칙 파일 경로"를 한 항목으로 추가한다. 원본의 경로 패턴(`src/gameplay/**`, `Source/**/Gameplay/**` 등)은 기능 폴더 이름을 전제로 하므로 **자기 코드 루트에 맞게 다시 쓴다.** 모듈이 하나이고 기능 폴더를 아직 정하지 않았다면 `Source/<모듈>/**/*.{h,cpp}`처럼 코드 루트 전체에 건다.

| 규칙 | 판단 |
|---|---|
| `test-standards.md` | 필수. 테스트를 쓰거나 결과를 해석할 때의 기준과 미실행 표기 |
| `gameplay-code.md` | 필수. 데이터 주도, 하드코딩 금지 |
| `network-code.md` | 멀티플레이일 때. 네트워크 폴더만이 아니라 코드 루트 전체에 걸어야 기능 폴더 안의 동기화 코드를 놓치지 않는다 |
| `design-docs.md` | GDD를 쓰기 시작할 때 |
| 엔진 씬·에셋 규칙 | 엔진이 정해진 뒤 해당 엔진용으로 작성 |
| `skill-authoring.md` | 스킬을 직접 고치기 시작할 때 |
| `agent-memory.md` | `memory: project` 역할을 쓸 때 |
| `ui-code.md`, `engine-code.md`, `data-files.md`, `shader-code.md` | 해당 코드가 생길 때 |
| `ai-code.md`, `narrative.md`, `prototype-code.md` | 해당 기능이 없으면 제외 |

### 6.4 helper 스크립트

| 파일 | 쓰는 곳 | 필요 |
|---|---|---|
| `hooks/yaml-helper.sh` | 모든 핵심 스킬의 설정 해석, session-start | 방법 A면 필수 |
| `scripts/engine-row.awk`, `engines/engines.tsv` | 엔진별 코드·테스트 루트 해석 | 필수 |
| `scripts/md-section.sh` | 문서의 필요한 절만 읽기·교체 | 필수 |
| `scripts/story-facts.sh`, `scripts/story-status.sh` | Story 필드 조회, 다음 Story 순서 | 필수 |
| `scripts/session-state.sh` | 체크포인트 교체·추가(`/checkpoint`, dev-story, story-done) | 필수 |
| `scripts/rotate-session-state.sh` | 긴 Notes를 로그로 이동(명시 실행) | 권장 |
| `scripts/review-receipts.sh` | 리뷰 이후 내용 변경 감지 | 필수(dev-story·story-done이 호출) |
| `scripts/adr-status.sh` | ADR 상태 조회 | ADR을 쓸 때 |
| `scripts/page-shot.sh`, `scripts/one-shot-*.sh`, `scripts/web-*.sh` | 브라우저 게임 경로 | Web 엔진일 때만 |

나머지 스크립트(스프린트, 버그 집계, 레지스트리, 단계 판정, 스킬 lint, 출시 등)는 해당 스킬을 들일 때 함께 가져온다.

**줄바꿈 고정:** helper를 들일 때 `.gitattributes`도 함께 가져온다. 최소한 `*.sh`, `*.awk`, `*.tsv`, `.claude/**/*.md`에 `text eol=lf`를 건다. Windows의 `core.autocrlf=true` 저장소에서 이 설정이 없으면 작업 사본은 멀쩡하다가 다음 clone·checkout 때 스크립트가 CRLF로 바뀐다. 그러면 Git Bash 훅과 helper가 깨지고, `engines.tsv`의 마지막 열에는 `\r`이 붙는다. 들인 뒤 `git check-attr eol -- <파일>`이 `lf`인지 확인한다.

### 6.5 템플릿과 참조 문서

| 파일 | 역할 |
|---|---|
| `templates/game-brief.md` | 한 장짜리 기획 입력. Features 체크박스와 Build order가 계획 역할을 한다 |
| `templates/story.md` | 수용 기준, 의존성, 변경 파일, 검증 결과 |
| `templates/session-state.md` | STATUS·CHECKPOINT와 자유 서술 영역 분리 |
| `templates/test-evidence.md` | UI·Visual 스크린샷과 수동 관찰 기록 |
| `docs/run-and-observe.md` | 엔진별 실행·스크린샷 절차 |
| `docs/error-recovery-protocol.md` | 실패·중단 시 보고 형식 |
| `docs/code-root-resolution.md` | 코드 루트 해석 |
| `docs/config-resolution.md`, `docs/automation-modes.md`, `docs/workflow-modes.md` | 방법 A일 때 설정 해석 계약 |
| `docs/coding-standards.md` | Story 종류별 증거 기준의 근거 문서. `story-done`·`run-and-observe`·`test-evidence` 템플릿이 참조한다. 참조용으로만 두고 CLAUDE.md에 import하지 않는다 |
| **`docs/engine-reference/<엔진>/`** (저장소 루트의 `docs/`, `.claude/` 밖) | 쓰는 엔진 폴더 하나. `create-stories`가 Story의 Risk 필드를 `VERSION.md`에서 읽고, `dev-story`의 엔진 검토와 엔진 역할이 API를 이 폴더와 대조한다. 없으면 Risk는 NOT ASSESSED가 되고 엔진 검토는 근거 없이 학습 데이터에 기댄다 |

ADR이나 버그 템플릿은 해당 스킬을 들일 때 추가한다. `templates/` 폴더 전체를 복사하지 않는다.

들인 뒤에는 설치한 스킬·역할·문서가 참조하는 `.claude/…` 경로를 모아 대상에 없는 것을 찾는다. 남은 누락이 minimal에서 실행되지 않는 분기(ADR, director gate)나 다른 엔진용인지 하나씩 확인한다.

---

## 7. 세션 복원

혼자 개발할 때 가장 효과가 큰 구성이다. 대화가 끊겨도 파일에서 작업을 이어 간다.

| 항목 | 내용 |
|---|---|
| 위치 | `production/session-state/active.md` (gitignore) |
| 자동 주입 범위 | CHECKPOINT 블록만. 최대 25줄, 3,000자 |
| 쓰는 방식 | CHECKPOINT는 **교체**, 그 아래 Notes는 추가. `session-state.sh`가 형식을 맞춘다 |
| 내용 | 현재 작업, 정확한 다음 행동, 막힌 점, 열린 질문, 관련 파일, 마지막 검증 결과, 브랜치 |
| 쓰는 주체 | `/checkpoint`(사용자가 실행할 때), `dev-story`·`story-done`(Story 작업이 끝날 때). 훅은 대화를 요약하지 않는다 |
| 이력 정리 | Notes가 200줄을 넘으면 `rotate-session-state.sh`로 `production/session-logs/`에 옮긴다 |

### 7.1 원할 때만 기록하기: `/checkpoint`

개발 질문, 조사, 잡담처럼 작업 상태가 바뀌지 않는 대화가 섞여도 체크포인트는 그대로 둔다. 기록은 직접 요청할 때만 한다.

| 명령 | 동작 |
|---|---|
| `/checkpoint` | 대화에서 현재 작업, 다음 행동, 막힌 점, 열린 질문을 뽑고, 브랜치와 변경 파일을 읽어 초안을 만든다. 초안을 보여주고 저장·수정·취소를 물은 뒤에 쓴다 |
| `/checkpoint <메모>` | 위와 같고, 메모를 초안에 반영한다 |
| `/checkpoint show` | 저장된 체크포인트만 보여준다. 아무것도 쓰지 않는다 |

- 스킬 위치: [.claude/skills/checkpoint/SKILL.md](../.claude/skills/checkpoint/SKILL.md). `disable-model-invocation: true`로 Claude가 스스로 호출하지 못하게 막았다.
- 질문만 오간 대화에서 실행하면 초안을 지어내지 않고 무엇을 기록할지 묻는다.
- `Run result:` 줄은 `/dev-story`만 쓴다. `/checkpoint`는 같은 Story를 이어 가는 경우에만 기존 줄을 그대로 옮기고, 아니면 뺀다. `/story-done`이 이 줄을 읽기 때문이다.
- 결과는 `SAVED — …(created/replaced/unchanged)` 또는 `NOT WRITTEN — <이유>`로 끝난다. 저장하지 않은 경우도 이유와 함께 드러난다.
- `dev-story`·`story-done`의 자동 갱신은 Story 작업에서만 일어나므로 남겨 두는 편을 추천한다. 이것까지 수동으로 바꾸려면 `dev-story/SKILL.md`와 `story-done/SKILL.md`의 체크포인트 단계, 그리고 각 `CONTRACT.md`를 함께 고친다.

수동 기록의 대가는 **저장을 잊으면 다음 세션이 오래된 상태를 복원한다**는 점이다. `Updated`와 `Branch` 줄로 오래된 체크포인트인지 판단하고, session-start는 브랜치가 다르면 한 줄 경고를 덧붙인다.

확인할 사례: 파일 없음, 종료 marker가 깨진 파일, 다른 브랜치에서 쓴 체크포인트, 중단된 작업. 정상 상태만 확인하면 복원이 실패하는 경우를 놓친다.

같은 체크아웃에서 세션 두 개를 동시에 열면 하나의 active.md를 두고 충돌한다. 병렬 작업이 필요하면 worktree를 나눈다.

---

## 8. 1인이어도 생략하지 않는 것

| 원칙 | 구체적 행동 |
|---|---|
| 실행해서 본다 | 플레이어가 보는 변경은 `commands.run`으로 띄워 확인하고 스크린샷을 `production/qa/evidence/`에 남긴다. 파싱·컴파일 성공은 실행이 아니다 |
| 면제와 통과를 구분한다 | `qa.level: minimal`의 테스트 면제는 PASS가 아니다. 테스트 0개, 미실행, 도구 없음은 NOT ASSESSED로 적는다 |
| 판정 순서를 지킨다 | BLOCKED > NOT ASSESSED > COMPLETE WITH NOTES > COMPLETE. 강제로 닫더라도 검증 판정은 그대로 남긴다 |
| 즉석 결정을 숨기지 않는다 | 설계에 없던 수치·표시·예외 처리는 `dev-story`가 Own calls로 보고하고 `story-done`에서 유지 여부를 정한다 |
| 쓰기 전에 확인한다 | `collaborative`에서는 파일을 쓰기 전에 묻는다. autonomous에서도 범위·삭제·스키마 변경은 묻는다 |
| 커밋은 요청할 때만 | `story-done` 끝의 커밋 제안은 Story 범위의 파일만 `git add -- <파일>`로 올린다. `git add -A`나 `.`은 쓰지 않는다 |
| 멀티플레이는 양쪽을 본다 | 네트워크 동작이 있는 Story는 호스트와 클라이언트 양쪽의 관찰을 수용 기준에 넣는다. 화면 한 장으로 동시성·타이밍을 증명했다고 보지 않는다 |

---

## 9. 빼는 것: 팀 단위 구성

빼는 것은 원본 삭제가 아니라 도입 목록에서 제외한다는 뜻이다.

| 구성 | 빼는 이유 | 다시 들일 조건 |
|---|---|---|
| director gate, `director-gates/` 정의 | solo가 전부 건너뛴다 | 외부 리뷰어가 생길 때 |
| 감독·리드·producer 역할 | 위와 같음 | 같음 |
| `team-*` 9개 스킬 | 여러 역할을 한 번에 조정하는 장치다 | 한 분야 작업이 반복되어 역할 분리가 실제로 필요할 때 |
| Agent Teams (`agent-teams.md`) | 여러 세션을 공유 작업 목록으로 엮는 실험 기능이다 | — |
| `sprint-plan`, `sprint-status`, `estimate`, `milestone-review`, `retrospective` | minimal에서는 brief의 Build order가 계획이다 | 마감과 일정 추적이 필요할 때 |
| `create-epics`, `gate-check`, `project-stage-detect` | create-stories가 얇은 Epic을 직접 만든다. 단계 판정은 팀 산출물 체크리스트다 | 문서 체계를 standard 이상으로 올릴 때 |
| 중앙 레지스트리(entities, TR), `create-control-manifest`, `consistency-check`, `review-all-gdds` | 문서가 적으면 이중 원본이 된다 | GDD·ADR이 여러 개로 늘어날 때 |
| `validate-*` 훅 대부분, 서브에이전트 로그 훅 | 공유 규칙과 다수 역할의 감사 장치다 | §5.2의 조건 |
| `project.local.yaml` 허용 목록, 두 개발자 충돌 규칙 | 협업자 간 설정 충돌 방지용이다 | 협업자가 생길 때 |
| `team-qa`, `qa-plan`, `regression-suite`, `test-flakiness`, `soak-test` | QA 조직과 대규모 테스트 운영이다 | 플레이 가능한 빌드가 쌓이고 회귀가 반복될 때 |
| 출시·운영(`release-checklist`, `launch-checklist`, `hotfix`, `day-one-patch`, `patch-notes`, live-ops, localize, community) | 출시 전에는 필요 없다 | 배포를 준비할 때 |
| 아트·UX 전체 경로(`art-bible`, `asset-spec`, `ux-design` 전 화면) | 자체 에셋과 UI 방향을 정하기 전에는 비용만 든다 | 해당 제작이 시작될 때 HUD 하나, 흐름 하나 단위로 |
| 다른 엔진의 역할·규칙·스크립트 | 쓰지 않는 엔진이다 | — |
| `studio-tests/`, `skill-test`, `skill-improve` | 프레임워크 자체 QA다 | 스킬을 직접 유지보수하기 시작할 때 |

---

## 10. 도입 순서

| 순서 | 할 일 | 끝났다고 볼 기준 |
|---|---|---|
| 1 | `project.yaml`, `CLAUDE.md`, settings의 deny 병합 | `commands.build`와 `commands.run`을 한 번씩 직접 실행해 결과를 확인함 |
| 2 | 세션 복원(§7): 축소 session-start, session-state, 템플릿, `/checkpoint` | `/checkpoint`로 저장한 내용이 resume·compact 뒤에 출력되고, 새로 연 세션에는 출력되지 않음. 질문만 오간 세션에서는 active.md가 바뀌지 않음. §7의 실패 사례도 확인함 |
| 3 | 핵심 경로(§6.1~6.5): brief → create-stories → dev-story → story-done | 실제 Story 하나를 구현, 실행, 스크린샷, 종료까지 진행함. 입력이 없을 때와 검증이 실패할 때 성공으로 보고되지 않음. 이 확인은 **대상 프로젝트 폴더에서 연 세션**에서 한다. 다른 폴더에서 연 세션에는 대상의 스킬·훅·deny가 로드되지 않고, `dev-story`·`story-done`은 `disable-model-invocation`이라 사용자가 직접 입력해야 한다 |
| 4 | 선택 스킬·역할·규칙 | 필요가 생긴 시점에 하나씩 추가하고, 추가할 때마다 그 스킬의 helper·템플릿을 함께 가져옴 |
| 5 | (선택) 방법 B로 이동 | 쓰는 스킬이 굳어진 뒤 모드 분기를 제거함. automation 분기와 검증 단계는 남아 있음 |

파일을 복사한 것만으로 도입이 끝난 것은 아니다. 각 단계는 실제 입력으로 한 번 돌려 보고, 성공한 경우와 실패한 경우가 구분되어 보고되는지까지 확인해야 끝난다.
