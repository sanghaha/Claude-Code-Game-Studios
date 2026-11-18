---
name: checkpoint
description: "사용자가 요청할 때만 production/session-state/active.md의 CHECKPOINT를 지금 작업 상태로 교체하거나, 저장된 체크포인트를 보여준다. 자동 실행하지 않는다."
argument-hint: "[show | 남길 메모]"
user-invocable: true
disable-model-invocation: true
allowed-tools: Read, AskUserQuestion, Bash(git branch --show-current), Bash(git status --porcelain*), Bash(bash .claude/scripts/session-state.sh *)
---

# Checkpoint — 원할 때만 세션 상태 기록

세션 상태는 이 스킬을 직접 실행했을 때만 기록한다. 훅은 체크포인트를 쓰지 않는다.
쓰는 파일은 `production/session-state/active.md` 하나다. 형식은
`.claude/docs/templates/session-state.md`를 따르고, 실제 쓰기는
`.claude/scripts/session-state.sh`가 맡는다. 다음 세션에서는 `session-start.sh`가
이 블록을 읽는다. `/story-done`은 이 블록의 `Run result:` 줄을 읽는다.

## 0. 인자에 따라 분기

- `show`: `bash .claude/scripts/session-state.sh show`를 실행해 그대로 보여주고 끝낸다.
  종료 코드가 4이면 "저장된 체크포인트 없음"이라고 알린다. 3이면 marker가 깨졌다고
  알린다. 어느 경우에도 파일은 쓰지 않는다.
- 인자 없음 또는 그 밖의 텍스트: 1단계로 간다. 텍스트는 사용자가 남기려는 메모로
  취급하고 초안에 반영한다.

## 1. 사실 수집

1. 브랜치: `git branch --show-current`. 실패하면 Branch에 `unknown`을 적고
   초안에 그 사실을 밝힌다.
2. 변경 파일: `git status --porcelain`. 이 대화에서 다룬 파일과 겹치는 것만
   Files in progress 후보로 삼는다. 다른 작업의 변경을 섞지 않는다.
3. 기존 체크포인트: `bash .claude/scripts/session-state.sh show`. 종료 코드 4는
   새로 만드는 경우이고, 3은 marker가 깨진 경우다. 3이면 쓰지 말고 5단계의
   NOT WRITTEN으로 보고한다.
4. 대화 내용: 현재 작업, 바로 다음 행동, 막힌 점, 열린 질문을 대화에서 찾는다.

**추측해서 채우지 않는다.** 이 대화가 질문과 답변뿐이었고 진행 중인 작업이 없다면,
초안을 지어내지 말고 무엇을 기록할지 사용자에게 묻는다. 대화에서 확인되지 않은
항목은 `unknown`이라고 적는다.

## 2. 초안 작성

```
**Updated:** [YYYY-MM-DD]
**Branch:** `[branch]`
**Current task:** [한 줄. 차갑게 읽어도 무엇인지 알 수 있게 파일·기능 이름 포함]
**Next step:** [한 줄. 바로 실행할 수 있는 구체적 행동]
**Blocked on:** [한 줄 또는 nothing]
**Files in progress:** [쉼표로 구분한 경로 또는 none]
**Run result:** [아래 규칙에 해당할 때만]
**Open questions:** [한 줄씩 또는 none]
```

- **Run result 줄:** 이 스킬은 Run result를 새로 쓰지 않는다. 이 줄은 `/dev-story`만
  쓴다. 기존 체크포인트에 Run result 줄이 있고 Current task가 같은 Story라면 그 줄을
  글자 그대로 옮긴다. 그렇지 않으면 줄 자체를 뺀다.
- **길이:** 25줄 이하. 결정 이유, 시도했다가 버린 방법 같은 긴 내용은 Notes 후보로
  따로 모은다.

## 3. 확인

초안 전체와, Notes에 덧붙일 내용이 있으면 그것까지 보여준 뒤 AskUserQuestion으로
묻는다.

- 저장
- 수정 후 저장 (고칠 점을 받아 초안을 다시 보여준다)
- 취소

답이 없거나 질문이 닫히면 저장하지 않는다. 같은 질문을 평문으로 다시 묻고 기다린다.

## 4. 쓰기

체크포인트는 stdin으로 넘긴다.

```bash
bash .claude/scripts/session-state.sh checkpoint <<'EOF'
[승인된 블록]
EOF
```

Notes에 덧붙일 내용을 승인받았다면 같은 방식으로 `append`를 실행한다.

## 5. 보고

결과는 아래 중 하나로 끝낸다. 출력의 `file` 행 상태값(created, replaced, inserted,
unchanged)을 그대로 인용한다.

- `SAVED — production/session-state/active.md (<상태값>, <N>줄)`. 25줄을 넘었으면
  다음 세션의 주입 비용이 커진다고 덧붙인다.
- `NOT WRITTEN — <이유>`. 사용자 취소, 답 없음, marker 깨짐(종료 코드 3), 쓰기
  실패(종료 코드 1), 기록할 작업 없음 중 해당 이유를 적는다. 종료 코드 3이면
  active.md의 `<!-- CHECKPOINT -->`와 `<!-- /CHECKPOINT -->` 줄을 직접 고쳐야
  한다고 알린다.

이 스킬은 커밋하지 않는다. `active.md`와 `production/session-logs/`는 gitignore 대상이다.
