---
name: brief-from-doc
description: "사용자가 직접 쓴 기획서(예: Doc/기획.md)에서 design/game-brief.md 한 장을 뽑거나, 기획서가 바뀐 뒤 brief에 빠진 항목을 제안한다. 새 컨셉을 제안하지 않는다. 아이디어가 없을 때는 /brainstorm이다."
argument-hint: "[기획서 경로]"
user-invocable: true
allowed-tools: Read, Glob, Write, Edit, AskUserQuestion, Bash(git hash-object *), Bash(bash .claude/scripts/md-section.sh list *), Bash(bash .claude/scripts/md-section.sh extract *)
---

# Brief from Doc — 기획서에서 brief 뽑기

사용자가 이미 쓴 기획서를 원본으로 두고, `/create-stories`가 읽는 한 장짜리
`design/game-brief.md`를 만든다. **기획서가 원본이고 brief는 요약본이다.** 기획서를
고치거나 brief로 대체하지 않는다. 새 아이디어, 컨셉 후보, 장르 제안은 하지 않는다.

**쓰는 파일:** `design/game-brief.md` 하나. 사용자가 초안을 보고 허락한 뒤에만 쓴다.
**읽는 파일:** 기획서, `.claude/docs/templates/game-brief.md`, `project.yaml`의 `project.name`,
있으면 기존 `design/game-brief.md`.
**다음:** `/create-stories`.

질문은 질문 도구로 한다. 도구가 없으면 선택지를 번호로 나열하고 턴을 끝낸다.
건너뛰거나 닫힌 질문은 답이 아니다. 아무것도 쓰지 말고 다시 묻는다.

```
- [ ] 1: 기획서 찾음, 읽음, 해시 기록
- [ ] 2: 새로 만들기 / 갱신 분기
- [ ] 3: 필드 채움, 출처 표시
- [ ] 4: 초안과 출처 표 제시, 쓰기 허락
- [ ] 5: 쓰기, 종료 블록
```

## 1. 기획서

- **경로:** 스킬 이름 뒤에 입력한 텍스트. 없으면 `Doc/*.md`, `docs/*.md`, `design/*.md`를
  Glob으로 찾되 `design/game-brief.md`와 `design/gdd/` 아래는 뺀다. 후보가 하나면 "이
  파일에서 brief를 만들까요: [경로]?"로 확인하고, 여럿이면 하나씩 선택지로 묻는다. 후보가
  없으면 멈춘다: `NOT WRITTEN — 기획서를 찾지 못했다. /brief-from-doc <경로>로 다시 실행한다`.
- **파일이 없거나 비어 있으면** 멈춘다: `NOT WRITTEN — <경로>를 읽을 수 없다(없음 | 빈 파일)`.
- **읽기:** `bash .claude/scripts/md-section.sh list <경로>`로 절 목록을 먼저 본다. brief는
  모든 절이 필요하므로 40KB 이하면 통째로 읽고, 그렇게 읽은 이유를 한 줄로 말한다. 40KB를
  넘으면 통째로 읽지 않고, 어느 절이 범위인지 사용자에게 묻는다.
- **해시:** `git hash-object <경로>`. 실패하면 `unknown`으로 적고 초안에 그 사실을 밝힌다.

## 2. 새로 만들기 또는 갱신

**`design/game-brief.md`가 없으면 새로 만든다.** 3단계로 간다.

**있으면 갱신 모드다. 다시 쓰지 않는다.**

1. 기존 brief의 `In your words` 절에서 `Source:` 줄을 찾는다. 기록된 경로와 해시가 지금과
   같으면 `UNCHANGED — 기획서가 brief를 만든 뒤 바뀌지 않았다`라고 알리고 끝낸다.
2. `Source:` 줄이 없으면 이 스킬이 만든 brief가 아니다. "기존 brief는 이 기획서에서
   만들어지지 않았습니다. 빠진 항목만 제안할까요?"라고 묻고, 아니라고 하면 아무것도 쓰지
   않고 끝낸다.
3. 기획서와 brief를 비교해 **추가 후보만** 뽑는다. 기획서에 새로 생긴 기능은 `[ ]`
   Features 후보로, 바뀐 수치·규칙은 해당 Features 줄의 수정 후보로, 새로 생긴 제외 항목은
   Out of scope 후보로 뽑는다.
4. 바꾸지 않는 것:
   - `[x]` 항목은 이미 구현된 것이다. 기획서와 달라졌으면 수정하지 말고 "구현과 기획서가
     다르다"로 보고한다.
   - `[-] … cut:` 항목과 `Keep` 절.
   - 사용자가 직접 고친 줄. 기획서에서 그 내용을 찾을 수 없는 줄은 사용자가 고친 것으로
     보고 건드리지 않는다.
5. 4단계에서 후보마다 before → after를 보여주고 한 번에 묻는다. 허락하면 `Source:` 줄의
   해시도 함께 갱신한다.

## 3. 필드 채우기

템플릿 `.claude/docs/templates/game-brief.md`의 절 순서와 **영어 heading·표시를 그대로**
쓴다. `/create-stories`, `/story-done`, `/cut`이 이 이름으로 brief를 읽는다. 내용은
기획서의 언어로 쓴다. 템플릿의 HTML 주석은 옮기지 않는다.

**출처 표시.** 각 값은 아래 셋 중 하나다. 표시 없는 값을 지어내지 않는다.

| 표시 | 뜻 |
|---|---|
| 표시 없음 | 기획서에 그대로 있다 |
| `*(inferred (strong))*` | 기획서의 여러 문장에서 직접 이끌어 냈다 |
| `*(guessed)*` | 기획서에 없다. 필수 필드를 비울 수 없어 제안한 값이다 |

**필드별 규칙:**

- **제목 `# Game Brief: <Working Title>`:** `/create-stories`가 이 제목을 slug로 바꿔
  epic 폴더 이름으로 쓴다. 영문으로 쓴다. 기본값은 `project.yaml`의 `project.name`이고,
  없으면 기획서 제목을 영문으로 옮긴 값을 `*(guessed)*`로 제안한다.
- **In your words:** 기획서를 복사하지 않는다. 다음 한 줄만 둔다.
  `Source: \`<기획서 경로>\` · git <해시> · <YYYY-MM-DD> — 원문은 이 파일이다. brief는 요약이고, 둘이 다르면 기획서가 맞다.`
  사용자가 이 대화에서 덧붙인 말이 있으면 그 아래에 원문 그대로 적는다.
- **Pitch, Genre, Controls, Hooks:** 기획서의 한 줄 소개·핵심 재미·조작 절에서 가져온다.
- **Question:** 기획서에 없으면 비우지 말고 `*(guessed)*`로 제안한다. 첫 빌드가 답해야 할
  질문 하나다.
- **Intent:** 기획서가 직접 말한 것만 쓴다(예: 인원 고정이면 `needs 2 players — from <경로>`).
  없으면 빈 줄로 둔다.
- **Look and references, First minute, Challenge:** 기획서에 있는 만큼만 쓴다. 없는 항목은
  템플릿의 `[...]` 자리표시를 지우고 빈 칸으로 둔다. 추측하지 않으며, 템플릿에 없는 새 표시도
  만들지 않는다. 비운 항목은 4단계의 "비운 필드"에 적는다.
- **Core loop:** 2~4개 bullet.
- **Goal & fail state:** 승리 조건과 실패 조건. `/create-stories`가 이 절을 인수 기준으로
  바꾸므로, 기획서의 수치와 조건을 빠짐없이 옮긴다.
- **Features:** `- [ ] <기능>: <결정적 규칙과 수치> (출처 절)` 형식으로 한 줄씩 쓴다.
  `/create-stories`는 brief만 읽고 기획서는 읽지 않는다. 그래서 줄에 규칙이 없으면 인수
  기준을 만들 근거가 없다. 수치, 시간, 개수, 조건을 줄 안에 넣는다.
  - **7개 이하.** 기획서의 기능이 7개를 넘으면 핵심 루프 없이도 성립하는 항목을 Out of scope
    후보로 제안하고, 어느 것을 미룰지 사용자에게 묻는다. 7개를 넘긴 채로 쓰지 않는다.
  - 전부 `[ ]`로 둔다. 코드가 있어도 구현 여부를 이 스킬이 판정하지 않는다.
- **Your systems:** 비워 둔다(빈 bullet 한 줄). 이 절을 채우면 `/create-stories`가
  thin/deepen Story와 `/design-system`을 요구하는 경로로 바뀐다. 초안 아래에 "Your systems는
  비워 두었다"라고 밝힌다.
- **Keep:** 비워 둔다. 플레이한 뒤에 채운다.
- **Avoid, Out of scope:** 기획서가 직접 제외하거나 금지한 것만 쓰고, 7개 제한으로 미룬
  항목을 더한다. 쓸 것이 없으면 빈 bullet 한 줄(`-`)로 두고 "비운 필드"에 적는다.
- **Build order:** 기획서가 순서를 정했으면 그대로 쓴다. 없으면 가장 위험하거나 재미를 아직
  증명하지 못한 것을 먼저 두는 순서를 `*(guessed)*`로 제안하고, 각 단계에 이유를 반 줄 붙인다.
  모든 Features 항목이 한 번씩 들어가야 한다.
- **Kill condition, Who it's for, Games you love / don't:** 기획서에 있을 때만 쓴다.
- **엔진과 project.goal은 다시 적지 않는다.** `project.yaml`에 있다.

**한 장 원칙.** In your words를 빼고 한 화면 분량을 넘으면, 다른 필드는 줄이고 Features
줄의 규칙은 지킨다.

## 4. 보여주고 묻기

초안 전체를 먼저 보여주고, 바로 아래에 출처 표를 붙인다.

```
| 필드 | 출처 | 표시 |
|---|---|---|
| Goal & fail state | §2.2, §2.3 | — |
| Build order | 기획서에 없음 | guessed |
```

그 아래에 네 줄을 붙인다. `guessed`인 필드 목록(없으면 `none`), 기획서에 없어 비운 필드
목록(없으면 `none`), Features 개수(N/7)와 미룬 항목, 그리고 "Your systems는 비워 두었다".

질문은 응답의 마지막에 따로 묻는다: "`design/game-brief.md`에 쓸까요?" 선택지는
`예 — 쓰기`와 `필드 먼저 고치기`다. 고치기를 고르면 지목한 필드를 before → after로 보여주고
다시 묻는다. Features 컷에 동의하거나 "계속"이라고 한 것은 쓰기 허락이 아니다.

## 5. 쓰기와 종료

허락을 받으면 `design/game-brief.md`를 파일로 쓴다(폴더가 없으면 만든다). 갱신 모드에서는
허락받은 줄만 Edit로 고친다. 기획서는 어떤 경우에도 쓰지 않는다.

응답의 마지막은 이 블록으로 끝낸다.

```
Verdict: [COMPLETE — brief written | COMPLETE — brief updated (N items) | UNCHANGED — source not changed | NOT WRITTEN — <이유>]
Source: <기획서 경로> · git <해시 | unknown>
Guessed: [필드 목록 | none]
Empty: [기획서에 없어 비운 필드 | none]
Next step: [/create-stories | 없음]
```

사용자가 쓰기 전에 멈추면 `NOT WRITTEN — 사용자가 쓰기를 승인하지 않았다`로 끝내고,
초안은 이 대화에만 남는다고 알린다.
