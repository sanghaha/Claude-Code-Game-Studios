# 구현 작업 절차 선택 이관 검토

작성일: 2026-11-11 (Asia/Seoul)  
원본 기준: CCGS v1.3.0, abc0de79d6ceb0b5d7aa693945849b3937afbe6a  
범위: 구현 작업 절차와 관련 버그·회귀·QA의 분석 및 이 보고서 작성

## 1. 결론과 판단의 경계

**지금 추천하는 핵심은 create-stories → dev-story → story-done이다.** Story의 수용 기준·범위·파일 목록·실행 관찰·완료 기록을 한 계약으로 가져온다. Unreal에 맞게 고친 뒤 선택 이관할 후보라는 뜻이며, 설치나 채택이 확정된 상태가 아니다. 최소 흐름에 create-epics, sprint-plan, gate-check, team-qa를 필수 선행 단계로 추가하지 않는다.

실행 검증은 이 문서 작성 범위가 아니다. 원본 스킬은 분석 자료로 읽었으며 실행하지 않았다. 엔진 실행·빌드·Automation·2인 검증·커밋·원본 변경·실제 이관은 하지 않았다. 이번 작성 파일은 이 보고서 하나뿐이다. **이관 제외는 원본 삭제를 뜻하지 않는다.**

| 판단 | 지금의 추천 |
|---|---|
| 최소 이관 묶음 M | create-stories의 SKILL.md·CONTRACT.md, dev-story의 SKILL.md·CONTRACT.md·references/engine-checks.md, story-done의 SKILL.md·CONTRACT.md. §5의 설정 해석·Story·체크포인트·증거 helper와 템플릿을 함께 가져온다. 모두 §6의 수정 후 사용 |
| 첫 구현 때 유용한 선택 묶음 U | 수정한 smoke-check 전체 7파일, code-review 2파일. formal sprint/QA 팀 없이 현재 빌드와 특정 Story를 확인하는 용도. M의 필수 선행 단계로 만들지는 않음 |
| 필요할 때 추가 | story-readiness, sprint-status의 minimal 진행 조회, bug-report, playtest-report, 작은 qa-plan. 버그가 쌓이면 bug-triage |
| 현재 이관 목록에서 제외 | create-epics·sprint-plan·gate-check·team-qa의 전체 운영 경로, regression-suite, test-flakiness, soak-test, test-helpers, 출시용 hotfix. 향후 조건 충족 시 재검토 |
| 엔진 분기 제외 | Web one-shot/grow/replay·npm·Playwright, Godot import/parser/gdUnit4, Unity assembly·capture helper·license CI. 공용 파일의 Unreal 부분까지 삭제하라는 뜻은 아님 |

M의 스킬 본문·계약 7파일만 복사하면 완성되지 않는다. 최소 필수 동반물은 yaml-helper.sh + engine-row.awk + engines.tsv, story-facts.sh, story-status.sh, md-section.sh, session-state.sh, review-receipts.sh, story.md·session-state.md·test-evidence.md 및 §5의 공통 참조 문서다. ADR/manifest 분기를 유지하면 adr-status.sh와 관련 참조를 추가한다.

현재 qa.level=minimal은 Logic/Integration 자동 테스트 파일을 필수로 만들지 않는다. **면제는 PASS가 아니다.** UI·Visual/Feel의 실제 보관 이미지, 구현 결과의 실행 여부, 네트워크 수용 기준의 호스트·클라이언트 양쪽 확인은 따로 기록해야 한다. 새 QA 설정을 덧써서 rigor의 파생값을 고정하지 않는다.

근거: create-stories/SKILL.md L31–38, L89–145, L270–324; dev-story/SKILL.md L67–75, L172–181, L252–269, L352–364; story-done/SKILL.md L74–89, L297–319; workflow-modes.md L123–155.

## 2. 확인한 원본·대상 전제

이하 상대 경로의 기준은 다음 두 실제 루트다. 원본 파일 근거의 L은 이 커밋 작업 트리에서 읽은 행 번호다. 제안 경로는 반드시 “신규 제안”으로 표시했다.

- 원본 S: [C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios)
- 대상 T: [C:/Users/Sangha_Remote/Documents/TrackBuilding2](C:/Users/Sangha_Remote/Documents/TrackBuilding2)

| 확인 항목 | 결과·의미 |
|---|---|
| 적용 지침 탐색 | C:/부터 사용자 홈·Documents·두 루트까지 AGENTS.md를 확인하고, 양쪽 트리에서 숨김 파일 포함 AGENTS.md를 검색했다. 발견하지 못했다. 양쪽 CLAUDE.md 및 이번 위임 지침을 읽었다 |
| 원본 기준 | git rev-parse HEAD가 지정 커밋과 일치. 원본의 추적되지 않은 ax-setup 참고 자료는 분석 기준 코드와 구분 |
| 대상 프로젝트 | TrackBuilding.uproject, Source/TrackBuilding.Target.cs, Source/TrackBuildingEditor.Target.cs, Source/TrackBuilding/TrackBuilding.Build.cs 실제 존재. 폴더명 TrackBuilding2에서 타깃명을 추정하면 안 됨 |
| 고정 설정 | T/project.yaml L6–25: Concept / hobby / minimal / collaborative, Unreal 5.8.4, C++, 코드 루트 Source/TrackBuilding, 예정 테스트 루트 Source/TrackBuilding/Tests |
| 파생 설정 | workflow=minimal, density=terse, QA=minimal, story_granularity=coarse, review=solo, team=individual. 별도 여섯 키를 쓰지 않고 현 설정 유지 |
| 실행 명령 | T/project.yaml L37–44에 build/test/run/smoke가 있음. PowerShell을 명시 실행하는 문자열. 경로·설정이 있다는 것과 성공 증거는 별개 |
| 현재 테스트 경로 | Source/TrackBuilding/Tests는 없음. 구성 예정값이며 실제 테스트 인프라·게임 테스트의 존재나 성공을 의미하지 않음 |
| 기획 기준 | T/CLAUDE.md L3–7, L26–38 및 Doc/기획.md §1, §4, §6–7. Steam PC, 리슨 서버 호스트 1 + 클라이언트 1. 서버 선착순 획득·배치 판정 |
| 기술 참고 | T/.claude/docs/unreal-framework-map.md 존재. 상태 소유권·RPC·복제·로컬 UI 기준과 설치 소스 근거를 보유 |
| technical-preferences.md | 요청에 이름이 있었으나 이번 실제 읽기에서 T/.claude/docs/technical-preferences.md는 없음. T/.claude/docs/에는 unreal-framework-map.md만 확인됨. 기존 영향 보고서 L24의 “두 문서 존재” 기록과 달라 현재 파일 상태를 우선함 |

대상 CLAUDE.md L32는 기획·설계 문서를 Doc/ 아래에 두도록 하고, L43–45는 project.yaml 명령 사용과 미실행 성공 주장 금지, 양쪽 화면 확인을 요구한다. 공통 지침은 이미 완료된 작업이므로 여기서 재작성하지 않는다.

참고 자료는 S/ax-setup/ccgs-adoption-reference.md와 T/ax-setup/ccgs-settings-skill-impact.md 및 .json이다. 후자의 minimal 후보와 직접 참조 목록을 실제 스킬·계약·스크립트와 대조했다. TrackBuilding에 대한 과거 판단을 TrackBuilding2의 확정 결정으로 승계하지 않았다.

## 3. 현재 최소 경로와 입출력 계약

### 3.1 입력 기획과 작업 기록의 위치

원본은 minimal에서도 design/game-brief.md를 요구하며, 파일이 없으면 create-stories가 중단하고 prototype/역문서화를 안내한다(L93–98). 대상의 Doc/기획.md가 이미 있으므로 이 안내를 그대로 가져오면 기존 Unreal 프로젝트를 웹 제작 또는 불필요한 역문서화로 돌릴 수 있다.

**추천:** Doc/기획.md를 원기획으로 유지하고, 선택한 기능·구축 순서만 담는 Doc/game-brief.md를 파생 문서로 추가한다(신규 제안, 현재 없음). 원본 game-brief 템플릿의 Features 체크박스·Goal & fail state·Build order 계약을 유지하고 각 항목에 Doc/기획.md의 절을 연결한다. 원기획을 요약본으로 덮어쓰지 않는다. 신규 문서 작성 자체는 이번 범위에서 하지 않았다.

작업·QA 기록은 원본 production/epics, production/qa, production/session-state 구조를 유지하는 편이 소비자 변경을 줄인다. 기획·설계 경로와 운영 기록 경로를 구분한 권고다. 모든 기록까지 Doc/로 옮기는 대안을 택한다면 §5의 하드코딩 소비자를 함께 바꿔야 한다.

| 단계 | 현재 최소 입력 | 출력·변경 범위 | 소비자·보장 |
|---|---|---|---|
| create-stories | 짧은 brief, 실제 engine_values/engine_row, coarse·terse·QA 값 | production/epics/<epic>/story-NNN-<slug>.md 신규 생성; EPIC.md 없으면 얇은 작업 묶음 생성; 기존 EPIC의 Stories 표 추가; 기존 index.md의 개수만 갱신, index 없으면 생성하지 않음 | dev-story가 Story 전체를 읽음. Requirement는 Brief MVP feature N, ADR/Manifest는 명시 N/A. test root가 예정 상태라는 점도 표시 |
| story-readiness(선택) | Story·brief·참조 ADR 및 의존 작업 | 파일 변경 없음. READY / NEEDS WORK / BLOCKED / NOT ASSESSED 및 수정 초안 | 독립 점검. 별도 “Ready 상태 쓰기” 기능이 아님 |
| dev-story | Story 전체·의존 상태·적용 엔진 사실 | Status=In Progress, Last Updated, 실제 변경 목록으로 Files 교체. 코드·테스트는 선택된 프로그래머 관할. 매 결과마다 active.md 체크포인트와 이미지 stamp 기록 | Implemented / INCOMPLETE / BLOCKED를 보고. 자기 판단으로 해당 Story를 Complete로 만들지 않음 |
| code-review(선택) | 실제 코드, 선택적으로 Story·참조 ADR | 파일 변경 없는 검토. 엔진 helper 결과 재확인 | APPROVED / APPROVED WITH SUGGESTIONS / CHANGES REQUIRED / NOT ASSESSED. 빌드·플레이 PASS의 대체물이 아님 |
| story-done | Story 기준·변경 파일·현재 Story와 일치하는 체크포인트·실제 증거 | 완료 여부를 먼저 보고, 종료 선택 후 Status=Complete와 Completion Notes·Own calls; EPIC 표; minimal Features 체크; sprint YAML은 이미 있을 때만 갱신 | 다음 Story를 story-status 순서로 안내. 완료 기록과 커밋은 별도 선택 |
| smoke-check(선택) | 현재 빌드·명령·실제 테스트 유무·중요 경로 | production/qa/smoke-<date>.md. 실행 결과·수동 확인·미실행을 분리 | 기본 minimal에선 선택이며 FAIL도 advisory. 실패를 PASS로 바꿀 수 있다는 뜻은 아님 |

근거: create-stories/SKILL.md L31–34, L99–145, L288–346; story-readiness/SKILL.md L23–38; dev-story/SKILL.md L101–128, L329–364; story-done/SKILL.md L321–401.

### 3.2 상태, 증거, 현재 작업과 커밋

| 표기/행동 | 원본의 실제 계약 | 이관 시 보존·보완 |
|---|---|---|
| Story 상태 | Ready/Blocked → In Progress → Complete. In Review는 조회·종료 후보로 지원하지만 dev-story가 자동으로 쓰는 단계는 아님 | 결과 문구 “Implementation Complete”와 파일 Status=Complete를 혼동하지 않음 |
| sprint YAML | backlog↔Not Started, ready-for-dev↔Ready, in-progress↔In Progress, review↔In Review, done↔Complete, blocked↔Blocked | minimal에 파일을 새로 만들 필요 없음. 선택 도입하면 두 기록을 대조 |
| 다음 작업 순서 | story-status.sh: IN_REVIEW → IN_PROGRESS → TODO(Ready/Not Started), 그 뒤 BLOCKED/OTHER/NO_STATUS, COMPLETE 개수 | 순서는 파일명 정렬 기반. 완료되지 않은 의존 작업을 이 helper가 그래프로 해결하지는 않음. 선택한 다음 Story의 Dependencies를 dev-story가 다시 점검 |
| 의존 작업 | dev-story는 미완료 의존에 중단·위험 수락·“이미 끝난 상태 정정” 선택을 제공. 마지막 선택은 의존 Story의 Complete를 승인 후 기록 | 실제 검증 없이 의존 상태 정정이 새 기능 성공 증거처럼 쓰이지 않게 사용자 진술·검증 방식 기록 |
| Logic/Integration | QA minimal에서 테스트 미작성·면제를 명시. 파일 존재와 테스트 통과를 구분 | 네트워크 동작은 해당 Story의 실제 2인 AC 확인을 별도 요구. QA 값을 올리는 변경이 아님 |
| Visual/Feel·UI | 모든 QA 수준에서 화면별 보관 이미지. Visual/Feel은 evidence 문서의 모든 sign-off 행 승인도 필요. solo가 직접 서명 가능 | 호스트·클라이언트 역할·시점·빌드를 붙임. 정지 이미지는 경쟁 처리·타이밍·연결 단절을 증명하지 못함 |
| Run result | dev-story의 마지막 응답과 active.md에 OBSERVED / NOT VERIFIED / N/A+구체 이유. story-done은 Current task가 같은 Story일 때만 사용 | Story 완료 기록에도 명령·결과·증거 경로를 남겨, 다음 체크포인트 덮어쓰기 후 증거가 사라지지 않게 함 |
| stamp | review-receipts.sh hash/check. CHANGED/GONE/RECEIPT:NONE은 원본에서 정보이며 판정 자동 변경 없음 | stale 여부를 PASS 근거로 삼지 않음. .uasset·Config 등 실제 동작에 영향을 준 변경 파일도 기록 대상 |
| Own calls | 설계에 없는 수치·표시·예외 동작을 dev-story가 보고하고 story-done이 유지/변경을 받음 | 최소 문서에서도 임의 설계 결정을 완료 기록으로 숨기지 않음 |
| 종료 판정 | BLOCKED > NOT ASSESSED > COMPLETE WITH NOTES > COMPLETE. UNTESTED는 미평가, DEFERRED playtest는 원본에서 비차단 | 핵심 2인 네트워크 AC를 단순 feel로 분류해 DEFERRED만으로 완료하지 않도록 구체 AC 작성 |
| 강제 종료 | 원본 story-done은 BLOCKED/NOT ASSESSED도 사용자의 별도 “close anyway” 선택으로 Complete 가능 | 종료 상태와 검증 판정 둘 다 보존. Complete 집계가 실제 2인 검증 완료를 뜻하지 않음을 명시 |
| 커밋 | story-done L352–356은 종료 후 매 모드에서 별도 “Commit this story?” 선택. Files·Story·종료 변경만 git add --, git add -A 또는 . 금지 | 커밋은 최소 이관의 자동 기능에서 빼거나 명시 요청 후 별도 단계로 유지. 이번 작업에서는 금지. unrelated 변경·이미 staged된 파일을 포함하지 않도록 사전 범위 확인 필요 |

근거: sprint-plan/SKILL.md L270–305; story-status.sh L8–34, L45–80; dev-story/SKILL.md L101–128, L252–269, L336–364; story-done/SKILL.md L116–197, L224–246, L297–356; test-evidence.md L70–90.

## 4. 관할 폴더의 전체 파일 판정

총 22개 폴더, 61파일이다. 지정된 12개 스킬 외 관련 절차는 bug-report, bug-triage, hotfix, regression-suite, qa-plan, team-qa, test-flakiness, soak-test, test-helpers, playtest-report를 포함했다. 아래 모든 경로는 **S/.claude/skills/** 아래 실제 파일이다.

- 추천: 수정 후 M에 포함.
- 선택: 가까운 작업에서 유용하지만 M은 이 파일 없이 진행 가능.
- 조건부: 명시된 기능·입력이 생긴 후 검토. 지금 M에서는 불필요.
- 현재 제외: 현 설정·범위에서 실행 경로가 없거나 비용이 커 현재 이관 목록에서 제외. 삭제 아님.

### 4.1 Story 계획·구현·종료

| 파일(S/.claude/skills/ 기준) | 판정 | 근거 위치 | 현재 최소 경로에서의 필요성·이유 |
|---|---|---|---|
| create-epics/SKILL.md | 현재 제외 | L57–83, L175–232 | brief에서 create-stories가 얇은 EPIC을 직접 생성. 별도 Epic 승인·아키텍처 분해 불필요. 여러 기능 묶음 경계가 필요하면 조건부 추가 |
| create-epics/CONTRACT.md | 조건부 | §Inputs Required, §Outputs, L25–32 | Epic 스킬 추가 시 필수 동반. minimal 예외와 테스트 DoD 모순 함께 수정 |
| create-epics/references/pr-epic-gate.md | 현재 제외 | L3–17 | full review에서 producer PR-EPIC만 호출. 현재 solo에서 로드하지 않음 |
| create-stories/SKILL.md | 추천 | L89–145, L270–324 | 원기획을 구현 가능한 coarse Story로 전환하는 진입. brief 경로·기획 추적·Unreal 증거 수정 필요 |
| create-stories/CONTRACT.md | 추천 | §Tier, §Output Guarantees, L71–92 | Story 필드·EPIC 표·번호·파일·엔진 위험 계약. minimal downstream 경로를 dev-story로 정정 |
| create-stories/references/qa-lead-gate.md | 조건부 | L3–29 | review_gate=run일 때 QA 명세. solo에서는 로드하지 않음. M만 가져오면 선택 분기와 링크도 비활성화해야 함 |
| story-readiness/SKILL.md | 선택 | L23–38, L98–109, L178–216 | 읽기 전용 사전 점검. 모호한 2인 AC/의존을 잡는 데 유용하나 원본 minimal 기본 경로 밖 |
| story-readiness/CONTRACT.md | 선택 | L20–25, §Hard Constraints, L57–79 | readiness 추가 시 함께 유지. 최소 예외·보장 수준과 상태 의존 구분 필요 |
| story-readiness/references/qa-lead-gate.md | 조건부 | L3–18 | full QA lead 검토. 현재 solo에서 제외 가능 |
| dev-story/SKILL.md | 추천 | L67–128, L130–206, L208–364 | 구현·실제 산출물·빌드·관찰·체크포인트의 중심. 웹 분기 제거, 2인 검증·자산 관할 보완 |
| dev-story/CONTRACT.md | 추천 | §Outputs, L66–81 | 부분 수정 허용 영역·미완료 인계 계약. L68–70·79의 무조건 테스트/전체 context 문구를 minimal 조건과 일치시킬 것 |
| dev-story/references/engine-checks.md | 추천 | L19–32 | Unreal Editor 타깃 빌드와 map 관찰. 타깃명을 TrackBuildingEditor로 확인하고 commands.build 우선 사용하도록 수정 |
| dev-story/references/version-mismatch.md | 조건부 | L7–23, L27–41 | Story가 실제 ADR/manifest를 참조하고 stamp가 다를 때만 필요. M에서 ADR 분기를 보존하면 함께 포함 |
| story-done/SKILL.md | 추천 | L74–107, L153–197, L266–401 | 증거·기준·설계 외 선택·완료 기록·다음 작업. 커밋과 판정 override 분리·증거 지속 기록 보완 |
| story-done/CONTRACT.md | 추천 | §Outputs, L78–93 | 완료≠테스트 PASS 계약. Next=gate-check 및 무조건 tests 경로 가정을 minimal과 맞춤 |
| story-done/references/review-gates.md | 조건부 | L3–9, L32–44 | full에서 qa-lead/lead-programmer gate. 현재 solo는 skip; QA minimal의 coverage gate도 skip |
| story-done/references/sprint-next.md | 현재 제외 | L3–29 | 실제 sprint plan 있을 때만 읽음. M에서는 story-status 순서 사용 |
| story-done/references/tech-debt-row.md | 조건부 | L3–30 | 사용자가 advisory를 tech debt로 기록하도록 고른 경우만. 등록부 경로와 소비자 형식 유지/수정 필요 |

### 4.2 진행 조회·코드 검토·스모크·테스트 구성

| 파일(S/.claude/skills/ 기준) | 판정 | 근거 위치 | 현재 최소 경로에서의 필요성·이유 |
|---|---|---|---|
| sprint-plan/SKILL.md | 현재 제외 | L94–100, L256–305, L341–385 | brief Build order가 계획. 도입하면 YAML 이중 상태와 QA plan gate까지 생김. 시간 상자 운영 필요 시 조건부 |
| sprint-status/SKILL.md | 선택 | L30–76 | sprint 없이도 15줄 이내 Story 진행률·다음 작업을 제공. 이름과 달리 minimal 조회 가능, 쓰기 없음 |
| code-review/SKILL.md | 선택 | L49–96, L147–181, L243–257 | solo에서도 명시 호출 가능. source review와 엔진 검토가 필요할 때. 40행/인터페이스 등 일반 휴리스틱을 UE 필수 위반으로 오용하지 않음 |
| code-review/references/lens-engine.md | 선택 | L3–11, L13–61 | 위 스킬의 한 엔진 reviewer가 읽는 필수 렌즈. GC/수명·복제·UMG 등 Unreal 기준 보완 |
| smoke-check/SKILL.md | 선택 | L30–35, L58–80 | M 필수는 아니나 첫 통합 동작부터 유용. current Story/epic scope·PC+2인 경로로 수정 |
| smoke-check/references/1-detect-setup.md | 선택 | L10–43, L62–90 | 실제 테스트와 체크리스트 구분, zero-test WAIVED 처리. 예정 test_root와 wrapper 실행파일 구분 수정 |
| smoke-check/references/2-run-tests.md | 선택 | L19–28, L105–158, L218–231 | Unreal 빌드 선행·Automation 출력 판독. no-test 경로도 최신 Editor 빌드 먼저 하도록 수정 |
| smoke-check/references/3-coverage.md | 선택 | L8–38 | no-test minimal에서는 skip. 테스트가 생기면 configured root/명시 Story scope 적용; UI 이미지와 문서를 구분 |
| smoke-check/references/4-manual-checks.md | 선택 | L16–60, L63–104 | launched 여부와 체크리스트 출처가 중요. generic menu/save보다 실제 2인 핵심 경로로 대체 |
| smoke-check/references/5-report.md | 선택 | L25–36, L75–85, L103–150 | FAIL/NOT ASSESSED/WAIVED/quick 라벨 보존. minimal에 “모든 Logic 테스트 있음”이라는 템플릿 오보고 방지 |
| smoke-check/references/6-write-gate.md | 선택 | L14–23, L49–108 | QA minimal은 FAIL advisory. 보고서의 실패 사실과 단계 게이트의 요구를 별개로 유지 |
| test-setup/SKILL.md | 조건부 | L20–29, L33–55, L59–65 | 첫 Automation 테스트가 실제 필요할 때. 최소는 테스트·CI scaffold 강제 없음 |
| test-setup/references/1-detect.md | 조건부 | L17–36 | 기존 파일 비덮어쓰기. Unreal test_root를 고정 Private/Tests 대신 현재 선언·예정 상태와 대조 |
| test-setup/references/2-plan.md | 조건부 | L13–39 | 생성할 실제 경로를 미리 나열. local tests와 CI를 별도 선택 항목으로 나누는 수정 필요 |
| test-setup/references/3-directories.md | 조건부 | L205–244 | UE Automation 설명 필요 시. 기존 모듈명·TrackBuilding. 필터·예정 test_root·minimal 면제 반영 |
| test-setup/references/4-ci.md | 현재 제외 | L120–182 | self-hosted Windows runner·UE_ROOT 전제. 로컬 테스트 준비에 GitHub Actions 통복사 불필요 |
| test-setup/references/5-smoke-seed.md | 조건부 | L8–38 | 새 checklist를 만들 때 재사용. 60fps/5분 memory를 확정 예산으로 복사하지 말고 2인 기능 경로로 교체 |
| test-setup/references/6-summary.md | 조건부 | L13–48 | 생성과 실행 검증을 구분하는 요약. CI 생략 시 “CI wired up” 완료 문구를 쓰지 않음 |
| test-evidence-review/SKILL.md | 선택 | L18–32, L106–147, L181–270 | 이미지·sign-off·자동 테스트의 내용 품질을 점검할 때. test_root 수정 및 assertion 개수 휴리스틱을 보조 신호로만 사용 |

### 4.3 단계 게이트

gate-check 전체는 현재 이관 제외다. 아래 “조건부”는 훗날 사용자가 단계 전환 판단을 선택했을 때의 내부 의존성이다. 이 보고서의 추천만으로 Concept을 변경하지 않는다.

| 파일(S/.claude/skills/ 기준) | 판정 | 근거 위치 | 현재 최소 경로에서의 필요성·이유 |
|---|---|---|---|
| gate-check/SKILL.md | 현재 제외 | L35, L54–71, L154–158 | minimal 기본 동선 밖. 명시 호출 시에도 stage 변경은 별도 확인; YAML+legacy 이중 쓰기 발생 |
| gate-check/CONTRACT.md | 조건부 | §Inputs, §Files Written, §Hard Constraints | gate 추가 시 판정·stage 쓰기 계약 필수. minimal 축약을 표 전체에 반영 |
| gate-check/references/2b-workflow-tier.md | 조건부 | L19–64, L77–126 | 문서 요구/QA 면제/smoke floor/override를 분리. zero-artifact false PASS 방지 |
| gate-check/references/3-run-gate-check.md | 조건부 | L23–87, L106–131 | artifact-check와 실제 내용·smoke 주장을 대조. Unreal 테스트 실행을 commands/helper로 명확히 연결 필요 |
| gate-check/references/4-assessment.md | 조건부 | L13–31, L36–68 | 수동 미확인 보존. 현재 solo에서는 director panel을 실행하지 않음 |
| gate-check/references/5-verdict.md | 조건부 | L38–63, L118–158 | FAIL→CONCERNS→NOT ASSESSED→PASS 순서, 미확인 필수 항목이 있으면 CONCERNS라도 stage override 불가 |
| gate-check/references/6-update-stage.md | 현재 제외 | L8–48 | set-stage.sh가 project.yaml+production/stage.txt를 쓰는 기능. 이번 고정 stage와 무관 |
| gate-check/references/7-next-steps.md | 조건부 | L18–22, §8 | minimal은 phase ladder 메뉴를 생략하고 paths.minimal로 안내. 전체 후속 스킬 통복사는 불필요 |
| gate-check/references/gate-systems-design.md | 조건부 | L24–33 | minimal은 brief 내용만. 현재 Doc 경로 매핑 필요 |
| gate-check/references/gate-technical-setup.md | 현재 제외 | L28–38 | minimal에서 not applicable+주석 PASS. 실제 UE 빌드 성공을 확인한 gate가 아님 |
| gate-check/references/gate-pre-production.md | 조건부 | L82–91 | minimal은 engine configured만 요구. 테스트/CI/ADR 강제 선행으로 오인하지 않음 |
| gate-check/references/gate-production.md | 조건부 | L70–86 | brief+Stories와 현재 build의 재미·end-to-end 확인. full slice 요구는 제거됨 |
| gate-check/references/gate-polish.md | 조건부 | L40–48 | minimal도 smoke+end-to-end+no S1 필요. smoke 선택 실행의 advisory와 이 gate의 PASS 요구가 다름 |
| gate-check/references/gate-release.md | 현재 제외 | L40–42 | 현재 Concept/hobby 구현 루프에 출시 전환 불필요. 향후 배포 범위에서 검토 |

### 4.4 버그·회귀·QA 관련 절차

| 파일(S/.claude/skills/ 기준) | 판정 | 근거 위치 | 현재 최소 경로에서의 필요성·이유 |
|---|---|---|---|
| bug-report/SKILL.md | 선택 | L20–32, L113–168, L176–208 | 재현 가능한 버그부터 유용. verify는 실제 관련 test 또는 지정 build 수동 재현 확인, close는 Verified Fixed 후. 네트워크 역할·양쪽 로그 추가 |
| bug-triage/SKILL.md | 조건부 | L27–41, L73–80, L93–130, L236–268 | 다수 버그 우선순위가 생길 때. minimal은 sprint 할당 대신 backlog/P1 다음 수정으로 운영 |
| hotfix/SKILL.md | 현재 제외 | L13, L72–93, L113–155, L159–248 | 출시 tag/branch·3역할 승인·QA 재진입·배포·backport 전제. 미출시 일반 버그는 Story로 수정 후 verify/close |
| regression-suite/SKILL.md | 현재 제외 | L45–60 | QA minimal이면 update/audit/report 모두 scan 전 NOT ASSESSED로 중단. 현재 설정에서는 가져와도 실행 결과 없음 |
| qa-plan/SKILL.md | 선택 | L41–46, L73–90, L197–201, L274–328 | 중요한 2인 기능만 feature/story 범위의 최소 수동 계획으로 사용 가능. 자동 테스트 행·sprint 요구를 되살리지 않음 |
| team-qa/SKILL.md | 현재 제외 | L31–64, L100–169, L213–258, L310–350 | 사람이 실행한 전체 사례·증거·sign-off를 모으는 큰 파이프라인. 첫 구현은 간단한 AC 기록으로 충분. 여러 Story 통합 QA 때 조건부 |
| test-flakiness/SKILL.md | 현재 제외 | L48–82, L114–128, L149–170 | 동일 코드 반복 실행 기록이 있어야 함. 3회 미만은 suspected. Unreal skip 근거가 없으면 매니페스트 등재만으로 비활성화됐다고 말하지 않음 |
| soak-test/SKILL.md | 현재 제외 | L20–40, L86–144, L273–287, L325–328 | 30분 이상 장시간 플레이가 가능한 빌드가 생긴 뒤. 프로토콜 생성과 사람이 실행한 관측을 구분 |
| test-helpers/SKILL.md | 현재 제외 | L25–34, L85–104, L323–375 | 실제 반복 테스트 패턴이 생긴 뒤 필요한 helper만. 빈 범용 factory·world helper 선도입 불필요; 경로·정리 수명 재검토 |
| playtest-report/SKILL.md | 선택 | L31–33, L105, L109–162 | 수동 2인 기록을 구조화할 때. new는 빈 템플릿 출력일 뿐 저장·실행 증거 아님. solo의 director gate는 생략 |

추가 참조 스킬 tech-debt는 위 61파일 집계에 포함하지 않은 조건부 소비자다. story-done의 “log as tech debt” 또는 lead-programmer 원본 preload를 유지할 때 필요하며 §5에서 별도로 추적한다.

## 5. 함께 가져와야 하는 실제 의존성

### 5.1 최소 런타임·형식

아래 경로는 모두 S 기준의 실재 파일이다. “함께”는 선택 이관을 할 때의 단위이며 이번 복사 지시가 아니다.

| 실제 파일·자료 | 필요 시점·전이 관계 | 조치 |
|---|---|---|
| .claude/hooks/yaml-helper.sh | 각 SKILL의 시작 resolve_config. Python 3 탐색·rigor 파생·specialists/engine_row 출력 | M에 필요. hooks 폴더의 라이브러리라는 이유로 전체 이벤트 훅을 설치하지 않음 |
| .claude/scripts/engine-row.awk + .claude/engines/engines.tsv | yaml-helper.sh L1426–1440, L2131–2168이 읽음. Unreal 기본 행의 test_root는 Source/<Module>/Private/Tests, commands는 모두 - | M에 필요. 프로젝트 명령은 T/project.yaml 우선. table을 명령 출처로 오인하지 않음 |
| .claude/scripts/story-facts.sh | dev/readiness/done: Story fields·Dependencies·상태·AC·lint. --sprint만 sprint-status.yaml의 file 항목 읽음 | M에 필요. L241 no_tr_id 관찰값은 minimal Brief 참조에는 정상 N/A이지 오류 확정 아님 |
| .claude/scripts/story-status.sh | done의 다음 Story, sprint-status/next-step 진행률. production/epics/*/story-*.md 단일 하위 깊이 | M에 필요. story-facts의 재귀 검색과 깊이가 다르므로 원본 한 단계 epic 구조 유지 |
| .claude/scripts/md-section.sh | set-field Status/Last Updated, Files 본문 교체, 조건부 ADR 절 읽기 | M에 필요. 필드명·## 절 이름을 한국어로 바꾸려면 소비자 전부 동시 변경 |
| .claude/scripts/session-state.sh + .claude/docs/templates/session-state.md | active.md 자동 생성·CHECKPOINT 교체·append. STATE/TEMPLATE 상수와 marker 계약(session-state.sh L14–55) | M에 필요. 모든 결과의 Current task/Next step/Run result가 핵심. 세션 시작 훅 없이도 수동으로 읽을 수 있음 |
| .claude/scripts/review-receipts.sh | dev hash → done/evidence-review check. 파일 byte hash와 실제 경로 목록 | M의 화면 증거에 필요. git/sha1sum/shasum 체인, 누락·stale를 관찰로 보고. 성공 판정기는 아님 |
| .claude/docs/templates/story.md | create-stories가 쓰고 후속 전체가 읽는 필드·절 계약 | M에 필요. QA minimal 문구와 resolved source/test root, Unreal 2인 증거 필드 보완 |
| .claude/docs/templates/test-evidence.md | Visual/Feel 서명 문서. UI는 이미지로 충분 | 해당 Story가 생기기 전 준비 추천. §Sign-Off L70–90의 optional row도 추가 후에는 전부 required로 읽히므로 임의 다역할 행을 만들지 않음 |
| .claude/docs/templates/game-brief.md | 신규 요약본을 만들 때의 형식 자료 | 원본 template만 참고/수정. 대상 Doc/기획.md는 덮어쓰지 않음. 신규 제안 Doc/game-brief.md의 구조 기준 |
| .claude/docs/config-resolution.md, automation-modes.md, workflow-modes.md | 설정·확인 범위·minimal 기본 동선의 의미 | 선택 절을 함께 유지. 기존 지침 재작성 불필요. settings 전체 통복사와 무관 |
| .claude/docs/code-root-resolution.md, coding-standards.md, run-and-observe.md | 소스/테스트 root, type별 evidence, 실행/이미지와 feel의 경계 | Unreal·minimal에 필요한 절만 병합/참조. 대상 framework-map을 우선 연결하고 legacy 기술 문서를 되살리지 않음 |
| .claude/docs/error-recovery-protocol.md, setup-requirements.md | 부분 실패·산출물 실제 존재 확인; Git Bash/Python 의존 설명 | 실제 도구 환경과 맞는 절만. Claude Code 전용 권한/주입 문법은 Codex에서 자동 동작한다고 가정하지 않음 |

기본 Bash/awk helper에는 외부 Python 패키지가 필요하지 않지만 yaml-helper의 설정 해석에는 Python 3가 필요하다. md-section/story-facts/session-state는 bash+awk 기반이고 review-receipts는 hash 도구가 필요하다. 이 분석에서는 helper도 실행하지 않았다. 실행 환경 검증은 향후 이관 작업의 완료 기준이다.

### 5.2 선택·조건부 의존성과 호출의 성격

| 선택 기능 | 실제 동반물·동적 입력 | 누락/제외 시 처리 |
|---|---|---|
| ADR/manifest 분기 유지 | .claude/scripts/adr-status.sh, dev-story/references/version-mismatch.md; adr-status.sh L88이 .claude/docs/templates/architecture-decision-record.md를 읽어 섹션 관찰. docs/architecture/adr-*.md·control-manifest.md·tr-registry.yaml은 게임 프로젝트가 만드는 동적 입력 | 현재 모두 필수 생성하지 않음. 분기를 잘라내면 호출·allowed-tools·계약도 제거. 분기를 남기면 helper와 template까지 포함 |
| checkpoint 회전 | .claude/scripts/rotate-session-state.sh: active.md narrative를 production/session-logs로 이동 | M의 직접 호출 의존은 아님. template이 약 200행 뒤 회전을 안내하므로 수동 유지보수용 조건부 동반 |
| 독립 코드 검토 | code-review/references/lens-engine.md + .claude/agents/unreal-specialist.md. 버전 검사 시 docs/engine-reference/unreal/VERSION.md·deprecated-apis.md·breaking-changes.md·current-best-practices.md, 해당 modules/plugins 파일 | 현지 5.8.4 사실과 충돌하는 reference는 검증 전 권위로 채택하지 않음. helper 미실행/형식 불량은 NOT ASSESSED |
| full Story gates | .claude/docs/director-gates.md 및 director-gates/ql-story-ready.md, ql-test-coverage.md, lp-code-review.md. qa-lead.md·lead-programmer.md | 현재 solo에서는 비활성. 참조 파일만 무조건 로드하거나 전체 조직 역할을 강제하지 않음 |
| create-epics/스프린트 | gdd-structure-check.sh·adr-status.sh·md-section.sh·write-guard.sh; producer.md, director-gates/pr-epic.md·pr-sprint.md. sprint 템플릿, milestone-definition.md·risk-register-entry.md는 선택 운영 입력 | write-guard의 overwrite 관찰 경로에 Web scaffold helper를 무조건 끌어오지 않음. producer 역할은 full gate 때만 |
| smoke-check | .claude/scripts/project-coherence.sh 직접 호출. test-run.sh는 원본 본문에서 Web에 직접 호출하나 실제 helper는 Unreal도 지원(§6.5) | 프로젝트 coherence는 버전·renderer·명령 관찰일 뿐 verdict 아님. Web 전용 web-detect.sh 호출은 Unreal에서 필요 없음 |
| test-setup | test-scaffold.sh의 원본 직접 호출은 Web 전용. UE 디렉터리·CI 텍스트는 references/3-directories·4-ci에 내장 | Unreal minimal을 위해 test-scaffold.sh 또는 Web scaffold 전체를 복사할 필요 없음 |
| qa-plan/team-qa | Story, 필요 시 QA plan·현재 smoke 보고서·open bugs·test-cases·실행 증거. team-qa가 qa-tester/qa-lead를 조건에 따라 호출, test-evidence.md 사용 | qa-plan에는 generic test-plan.md를 직접 사용하라는 계약이 없음. 보고서 한 장 생성과 QA 실행 완료를 구별 |
| bug-report | 자체 내장 템플릿을 쓰며 verify는 commands.test 또는 named build 수동 재현 | 별도 templates/bug-report.md·bug-closure-record.md는 이 SKILL의 직접 필수 참조가 아님. 중복 형식을 독립적으로 복사해 drift시키지 않음 |
| playtest-report | 자체 내장 양식, full일 때 creative-director.md + director-gates/cd-playtest.md | solo에서는 director gate 생략. 별도 플레이테스트 기록 생성이 필요한 경우만 |
| tech debt | story-done/references/tech-debt-row.md → docs/tech-debt-register.md. .claude/skills/tech-debt/SKILL.md §Debt Register Format이 소비 | 이 선택을 유지하면 Added 열 등 형식 일치. Doc로 변경할 경우 tech-debt·lead-programmer도 함께 연결 변경 |
| gate-check | artifact-check.sh → .claude/docs/workflow-catalog.yaml; gdd-structure-check.sh·adr-dep-graph.sh. stage 쓰기는 set-stage.sh → project-state.sh → yaml-get.awk (+ engine-row.awk/engines.tsv) | M에서 불필요. catalog는 산출물 관찰용이므로 gate tier를 자동 확정하지 않음 |
| set-stage의 빈 프로젝트 분기 | project.yaml이 없을 때만 .claude/hooks/update-check.sh의 UPDATE_CHECK_VERSION을 읽음(set-stage.sh L107–108) | T에는 YAML이 있으므로 현재 의존 아님. 일반 재사용 템플릿으로 무설정 생성까지 지원할 때만 검토 |
| 출시 hotfix | lead-programmer·qa-tester·producer·qa-lead, smoke-check/team-qa, bug-report verify/close, templates/incident-response.md | 출시 승인·tag·merge·deploy·backport 기능은 이번 구현 루프에서 제외 |

설명 링크, 허용 도구 항목, 실제 호출, 조건부 preload는 동일한 의존성이 아니다. 예를 들어 test-run.sh 전체를 Web 전용이라고 제외하면 향후 UE runner 관찰 기능도 잃는다. 반대로 test-scaffold.sh web 호출이 있다는 이유로 Web scaffold를 최소 묶음에 추가할 필요도 없다.

### 5.3 프로그래머와 Unreal 역할

| 역할·실제 파일 | 원본 선택 조건·전이 의존 | 대상 추천 |
|---|---|---|
| .claude/agents/engine-programmer.md | dev-story L137: Foundation layer 우선. 역할 L103–107은 gameplay 구현·build infra 소유를 제한 | 초기 게임 프레임워크 기능을 엔진 자체 수정으로 오인하지 않도록 Story 소유자를 명시 |
| gameplay-programmer.md | Visual/Feel 또는 Core/Feature gameplay. L79–80, L115의 무조건 단위 테스트 문구가 dev-story의 minimal 면제와 충돌 | 핵심 구현 역할 후보. 테스트가 선택인 경우 그 이유를 briefing과 역할에 동일하게 적용 |
| network-programmer.md | Core/Feature networking. 서버 권한·세션·복제 등 | 이 게임에서 중요. 한 Story 안의 gameplay+network 경계는 주담당 1명과 검토 관점으로 분리해 중복 쓰기 방지 |
| ui-programmer.md | UI story | UMG/HUD 구현이 시작될 때. 로컬 UI와 서버 규칙 상태를 framework-map에 따라 구분 |
| ai-programmer.md | AI/pathfinding story일 때만 | 아직 경로 탐색 구현 방식 미확정. 기차 경로가 있다는 이유만으로 필수 역할에 넣지 않음 |
| unreal-specialist.md | dev-story L144–153 및 yaml-helper.sh L2094–2128: primary는 engine.name에서 파생. 대상 specialists block이 없어도 Unreal primary는 선택됨 | 코드 Story의 엔진 검토 역할. 존재하지 않는 “ue-cpp-specialist”를 만들거나 이름을 추정하지 않음 |
| ue-replication-specialist.md | Unreal specialist의 실제 하위 역할. networking reference를 읽음 | 2인 권한·RPC/relevancy의 어려운 부분에서 조건부. client prediction을 무조건 전체 시스템에 도입하는 규칙은 아님 |
| ue-blueprint-specialist.md | BP/C++ 경계·binary assets·Blueprint compile 관점 | BP assets를 만드는 Story에서 조건부. source file edit로 .uasset/.umap 조작 금지, 실제 editor compile 증거 |
| ue-umg-specialist.md | UMG/CommonUI 관점, UI 관련 references | HUD/메뉴 필요 때만. CommonUI 도입 자체를 이번 보고서가 확정하지 않음 |
| ue-gas-specialist.md | Unreal specialist가 GAS 도메인에 위임 가능 | 현재 트랙 빌딩 기획에 GAS 채택 결정 없음. “combat은 모두 GAS” 일반 규칙을 가져오지 않음 |
| lead-programmer.md·qa-lead.md | full gates 또는 선택 QA. lead skills preload=[tech-debt], qa-lead preload=[bug-report, release-checklist] | 원본 role 통복사는 출시/부채 스킬까지 전이됨. 최소 role로 줄이거나 preload를 선택 기능으로 분리 |
| qa-tester.md | team-qa individual에서도 실행 사례 작성 담당. 반복적인 자동 테스트 권고 존재 | 선택 QA에만. tests 면제와 2인 수동 evidence 계약 반영 필요 |

원본 individual은 “모든 agent가 사라짐”이 아니다. dev-story의 programmer+engine check는 team.size를 조회하지 않으며, code-review도 solo에 의해 자동 생략되지 않는다. team-qa는 individual에서 qa-tester와 자체 phase gate의 qa-lead를 사용하고 studio에서만 per-story 병렬 폭을 늘린다(team-qa L31–96). 이는 원본 동작 분석이며, 이번 분석에서 에이전트를 생성하거나 역할을 실행하지 않았다.

## 6. Unreal용 수정 목록: 위치·문제·제안·소비자 영향

### 6.1 기존 기획을 minimal 입력으로 연결

- **위치:** create-stories/SKILL.md L93–145, L297–310, story-readiness L116–119, story-done L345–349; workflow-catalog.yaml의 paths.minimal, next-step·cut·brainstorm의 brief 소비.
- **문제:** 원본 design/game-brief.md 하드코딩은 T/Doc/기획.md를 못 읽는다. “문서 없음→prototype” 안내는 Unreal 작업을 잘못 돌릴 수 있다. minimal L145의 “GDD/ADR 읽지 않음”과 L135–138의 deepen GDD 참조도 그대로는 해석이 엇갈린다.
- **제안:** Doc/game-brief.md(신규 제안)에 현재 선택 feature·목표·실패·Build order와 원기획 절을 연결. 없는 세부 수치·규칙은 발명하지 말고 해당 Story의 질문으로 해결. 기존 ADR을 실제로 참조하면 minimal이라도 그 결정 상태는 검토한다.
- **영향:** story Context의 GDD 경로와 Requirement, done Features tick, readiness trace, next-step/cut/catalog가 같은 경로·항목 식별자를 사용해야 한다. 숫자 N을 쓰는 동안 Features 재정렬을 피하고, 재정렬 시 해당 Story 참조를 일괄 대조한다. stable ID로 바꿀 경우 모든 소비자를 함께 바꾼다.

### 6.2 테스트 루트의 “예정”과 “실존” 분리

- **위치:** yaml-helper.sh L1443–1485, L1625–1652; code-root-resolution.md §Resolution; engines.tsv L4. test-setup/references/3-directories.md L205–244, smoke detect L23–43, coverage L19–24, evidence-review L112–119, regression-suite L91–96.
- **문제:** resolve_test_root는 override를 디렉터리 존재까지 검사한다. 현재 Source/TrackBuilding/Tests가 없으므로 “invalid/no folder” 관찰이 예상된다(스크립트 실행으로 확인한 결과가 아니라 코드에서 읽은 예상). 이를 엔진 미설정으로 처리하거나 Private/Tests를 새 기본값으로 쓰면 현 설정을 깨뜨린다.
- **제안:** 선언값(예정 root)과 실제 존재 상태를 출력에서 구분. QA minimal의 test 부재는 WAIVED; code root의 진짜 오타·부재는 구현 차단. 향후 test-setup을 선택했을 때 예정 root를 만들고 첫 테스트를 실제 discovery로 확인한다. 현재 YAML·폴더는 변경하지 않는다.
- **영향:** test-setup·smoke·evidence-review·bug verify·regression·test-helpers·qa-tester 및 directory 문서/Story template fallback이 모두 configured root를 사용해야 한다. Source/<Module>/ 안의 Private/Tests는 원본 관례이지 대상의 확정 필수 경로가 아니다.

### 6.3 빌드·Automation·화면·2인 플레이를 다른 결과로 기록

- **위치:** dev-story/references/engine-checks.md L19–32; smoke-check/references/2-run-tests.md L105–158, L218–231; run-and-observe.md §Unreal 5, §Run result; T/project.yaml L41–44.
- **문제:** no-test minimal 경로는 commands.smoke를 build보다 먼저 택한다. 대상 smoke는 nullrhi 기동 후 Quit이며 C++ 최신 컴파일 명령이 아니다. 단일 run은 2인 접속 명령도 아니며 nullrhi Automation은 화면·Steam·동시 상호작용 성공 증거가 아니다.
- **제안:** code Story는 commands.build로 TrackBuildingEditor 최신 build를 확인한 뒤 smoke/Automation을 목적별 실행. commands.test는 실제 게임 테스트가 있을 때 사용하고 TrackBuilding. 필터·실행 개수·실패 이름·완료 로그·출력 보고서를 기록. 현재 smoke는 “headless boot”로 정확히 표시한다.
- **영향:** dev/done checkpoint·Completion Notes·smoke report·QA plan·bug verify에 Build, Automation, Headless boot, Visual observation, Listen-server 2인, Steam 각각 PASS/FAIL/NOT RUN/NOT ASSESSED/N/A와 근거를 기록. 한 결과로 다른 행을 채우지 않는다. 0 tests·아무 Success line 없는 exit 0·과거 report는 PASS가 아니다.

### 6.4 PowerShell 명령과 실제 프로젝트명 우선

- **위치:** dev engine-checks L24–29, smoke runner L111–142, test-setup CI L147–178, run-and-observe.md L174–207.
- **문제:** 원본은 Git Bash+UBT.exe 기준(일부 UE 5.7 실행 기록)이고, T는 명시 PowerShell+Build.bat 문자열이다. 폴더 TrackBuilding2를 <Project>로 넣으면 타깃·uproject 경로가 틀린다. commands.test 첫 실행파일 powershell.exe를 Unreal editor로 오인할 수도 있다.
- **제안:** 프로젝트의 commands.build/test/run/smoke를 그대로 우선하고 외부 shell에 안전하게 전달하는 단일 실행 경계를 정한다. PowerShell의 exit $LASTEXITCODE 의미를 보존한다. engine.path는 editor 파일이 아니라 UE root다. 엔진 검사는 Build.version와 실제 binaries에서 확인한다. Bash를 유지할 때만 MSYS map URL 변환·timeout을 별도로 처리한다.
- **영향:** 명령 탐지·project-coherence·test-run·CI·캡처 안내 전체에 shell/project/module 별도 필드가 필요. 본 분석에서 명령 실행·버전 변경·명령 재저장은 하지 않음.

### 6.5 공용 test-run helper의 선택적 재사용

- **위치:** .claude/scripts/test-run.sh L15–39, L280–299, L368–379, L614–625.
- **문제:** helper 자체는 unreal을 지원하나 smoke 본문의 직접 helper 호출은 web뿐이다. UE prebuild는 commands.build가 아니라 UBT 명령을 내부 생성한다. wrapper-aware 설정과 자동 일치하지 않는다.
- **제안:** UE runner를 한곳으로 모으기로 선택하면 commands.build 우선 또는 검증된 prebuild 인계로 보완하고 smoke/bug verify에서 같은 관찰 형식을 사용. 이미 확인한 최신 build를 생략할 경우에만 명시적으로 --no-build를 사용한다. 무조건 이식할 이유는 없다.
- **영향:** test-run.sh와 yaml-get.awk가 동반되고 Unreal 출력 ladder의 Fail/no-match/Success/timeout 분기를 보존. Web 전용 page-shot 등은 UE 실행 경로가 사용하지 않으면 제외. 스크립트 출력은 관찰이며 최종 verdict는 호출 스킬이 결정한다.

### 6.6 최소 2인 기능 확인을 AC에 포함

- **위치:** create-stories L197–208, L310–324; dev-story L252–269; story-done L122–137, L182–189; T/Doc/기획.md L138–155, T/CLAUDE.md L43–45.
- **문제:** 단일 화면의 “OBSERVED”나 코드 검색만으로 서버 선착순·클라이언트 상태 일치를 확인할 수 없다. 원본의 DEFERRED feel·qa minimal 면제를 이용해 네트워크 핵심 결과도 미룰 위험이 있다.
- **제안:** 네트워크 Story마다 해당되는 최소 사례만 AC로 구체화: 양쪽 이동/표시, 동일 조각 동시 획득, 동일 위치 동시 배치, 서버 판정 후 양쪽 조각·소지 상태 일치, 라운드 전환·승패 일치, 호스트 종료 시 전체 플레이 종료. 아직 범위 밖인 Steam 세션은 N/A/NOT RUN과 이유로 남긴다.
- **영향:** Story type이 Logic이라도 네트워크 surface가 있으면 2인 관찰이 필요하다. 머리글 Type 하나가 evidence 범위를 삭제하지 않도록 혼합 AC별 증거를 연결. 신규 정책 knob 없이 현지 기획과 검증 규칙을 실행 가능하게 표현하는 보완이다.

### 6.7 C++ 밖의 Unreal 산출물과 검토 관점

- **위치:** dev-story L186–190, code-review L108–143, lens-engine L25–61, unreal-specialist.md L82–94, L111–122.
- **문제:** “code root 밖 쓰기 금지”만 적용하면 Config/*.ini, Content/*.uasset/.umap, .uproject, Build.cs/Target.cs가 필요한 변경을 표현하지 못한다. 반대로 Web data/config.json 규칙을 UE 데이터의 필수 위치로 오용할 수 있다. UE Framework의 GameState/Subsystem을 일반 singleton 금지 규칙으로 잘못 판정할 수 있다.
- **제안:** Story Files에 소스·설정·에셋의 실제 계획 경로·관할 도구를 구분. 코드 변경은 code root, asset 변경은 editor/MCP/commandlet 경로로 처리하고 Blueprint compile/save 결과를 기록. 수치 위치는 대상 framework-map의 DataAsset/DataTable/적절한 설정·BP 조정 원칙으로 결정한다. 무조건 GAS·CommonUI·pooling·custom prediction을 요구하지 않는다.
- **영향:** programmer brief·engine lens·scope 비교·evidence stamp·커밋 목록에 같은 파일 집합을 사용. binary package를 텍스트 diff로 검증했다고 보고하지 않는다. 테스트 추가 시에도 일회성 범용 helper를 먼저 만들지 않는다.

### 6.8 원본 계약·양식의 불일치 정리

| 원본 위치 | 현재 문제 | 변경안·영향 |
|---|---|---|
| dev-story/CONTRACT.md L68–70, L79 | 전체 TR/ADR/manifest context·무조건 Logic/Integration test를 가정. 앞부분 minimal 면제 및 SKILL L173–175와 충돌 | workflow/qa 축별 조건을 명시. 최소 경로를 전체 설계·테스트 경로로 승격하지 않음 |
| create-stories/CONTRACT.md L71–85 | Next가 readiness이고 date/TR/ADR을 보편 전제로 서술 | minimal은 dev-story, Brief 참조·N/A 허용을 소비자 보장에도 반영 |
| story-done/CONTRACT.md L78–85 | Next=gate-check, tests/unit 경로와 존재 전제가 minimal default와 어긋남 | 다음 Story 선택이 기본, gate는 선택. Complete는 suite pass 증거가 아님을 유지 |
| create-epics/SKILL.md L221–232 | minimal 특례가 governance 표는 빼지만 DoD의 “passing tests” 행은 별도 면제하지 않음 | 도입할 경우 qa.level 조건 적용. 현재는 스킬 자체 제외 |
| smoke coverage/report L21–38 / L75–85 | tests/unit fallback, UI도 문서 존재를 coverage로 삼음. 면제 경로에 “모든 Logic/Integration 테스트 있음” 템플릿이 남음 | test_root·실제 이미지·WAIVED 구분. quick/skip은 미확인 그대로 |
| qa-plan/SKILL.md L124–141, L280–288, L318–322 | minimal에도 systems-index/GDD mining 기대, 끝부분 무조건 테스트 생성·코드 리뷰 안내 | Story AC와 brief를 기준으로 읽고 minimal 자동 테스트 행·종료 안내 일치. 선택 review를 필수 gate로 만들지 않음 |
| gameplay-programmer.md L79–80, L115; qa-tester.md §Automated Test Writing | 역할의 무조건 테스트 권고가 skill waiver와 충돌 | briefing의 resolved qa.level과 현지 증거 규칙을 우선하도록 역할도 수정 |
| bug-report/SKILL.md L124–129 | “re-run reproduction”의 첫 단계가 Grep/Glob이며 Bash 테스트 실행 절차와 frontmatter의 허용 도구가 불일치 | 코드 경로 분석과 실제 재현을 명확히 구분. UE runner 권한은 필요한 범위만 연결, 수동 named build 확인은 유지 |
| sprint-plan/SKILL.md L351–371 vs gate-polish L40–48 | 모든 흐름에서 QA plan/sign-off가 단계 필수인 듯 안내하나 minimal/standard의 실제 gate 축약은 다름 | 선택 도입 때 실제 tier에 맞춘 안내. 현재 sprint 도입 이유로 사용하지 않음 |
| test-setup/references/3-directories L64–77, 6-summary L48 | generated README가 무조건 tests/CI 성공을 선언 | 파일 생성·실제 compile/discovery·CI 연결/실행 상태를 따로 표시 |

이 수정은 원본 파일 수정 지시가 아니라 향후 대상용 변형안이다. SKILL.md만 수정하고 CONTRACT.md·template·role·helper를 그대로 남기면 같은 불일치가 재발한다.

### 6.9 엔진 참고 문서·설정은 통복사하지 않음

S/docs/engine-reference/unreal/VERSION.md L5–10은 5.8·설치 버전 NOT DETERMINED이며 일부 내용이 5.7 기준이라고 직접 적혀 있다. T의 5.8.4 설정·설치 소스 기반 framework-map과 같지 않다. L36의 Iris “production-ready” 표현도 T/framework-map L53의 “현재 기본 legacy, Iris는 별도 opt-in”과 섞으면 활성 복제 방식에 대한 잘못된 결론을 낼 수 있다. 출시 상태 표기와 프로젝트 활성화 상태는 별도 사실이다.

따라서 VERSION.md나 networking 문서를 근거 없이 덮어쓰거나 대상 버전을 바꾸지 않는다. 필요한 UE API만 T/framework-map과 설치된 5.8.4 소스에서 대조하고 확인 경로·미확인을 기록한다. technical-preferences.md 부재를 원본 placeholder 파일의 재설치 사유로 삼지 않는다. project.yaml의 기존 값이 우선이며 fallback에 필요한 미확정 항목은 미확정으로 남긴다.

project.yaml, .claude/settings.json, settings.local.json, hooks·rules 전체를 통복사하지 않는다. 특히 permissions·Bash allow·hook 이벤트·모델 지정·agent preload는 절차 파일의 실행 의존성과 별도 이관 결정이다. 기존 프로젝트 공통 지침과 source/asset 보호 정책은 해당 담당 단위와 충돌하지 않게 필요한 연결만 검토한다.

## 7. 향후 실행 검증을 연결할 지점

아래는 이관 후 확인할 목록이며 이번 실행 결과가 아니다.

| 시점 | 확인할 것 | 최소 기록·완료 기준 |
|---|---|---|
| Story 생성 | 원기획 절, 선택 feature, scope, 의존성, 실제 명칭·경로 | coarse 5–10 AC가 목표이나 숫자 채우기용 기능을 발명하지 않음. readyness 최소 AC는 3/2/1로 다른 목적임. 미확정 수치·세션 결정은 해결할 질문으로 표시 |
| 구현 착수 | source root 유효, ADR을 실제 참조하면 Accepted, 의존 작업 상태 | qa minimal에 없는 테스트 root만으로 코드 구현을 차단하지 않음. 필요한 Config/Content 범위는 Story에 명시 |
| 코드 변경 후 | T/commands.build의 TrackBuildingEditor build | 실제 실행 시 명령·시각·대상·exit code·오류 로그를 보관. 미실행은 NOT RUN/NOT VERIFIED |
| 테스트가 있는 경우 | T/commands.test, TrackBuilding. discovery, 결과 개수·성공/실패 | 0건·미완료·timeout을 PASS로 읽지 않음. Saved/Automation·Saved/Logs는 실행 증거의 출처이며 원본 소스가 아님. 오래된 report를 이번 결과로 쓰지 않음 |
| 화면이 있는 Story | 실제 map/상태 도달, 관련 화면 캡처 후 직접 열어 확인 | production/qa/evidence/<story-slug>/에 역할·시점별 이미지, hash stamp. timing/feel은 정지 이미지로 검증했다고 하지 않음 |
| 네트워크 변경 | 리슨 서버 호스트+클라이언트 2명으로 해당 AC 실행 | 빌드/branch와 로컬 PIE·별도 프로세스·LAN·Steam 중 실제 환경, 각 역할, 재현 절차, 양쪽 관찰과 로그를 기록 |
| Steam 세션 Story | 실제 Steam 친구 초대/로비·입장·종료 등 선택한 기획 범위 | LAN/로컬 접속을 Steam 검증으로 표기하지 않음. 계정·호스트/클라이언트 실행 환경·온라인 API 선택은 먼저 구체화 |
| story-done | 모든 AC에 관찰·사용자 실제 테스트 진술·정적 사실·미확인을 연결 | QA 면제/미평가/실패/보류/override를 다른 값으로 기록. 종료·review 출처·Own calls·다음 작업 확인 |
| 버그 수정 | 기존 BUG-ID 재현 사례를 고친 build에서 다시 실행 | 자동 test가 없으면 수동 named build로 verify. Verified Fixed 후 close. 변경 코드 검색만으로 fixed 확정 금지 |

기차·트랙처럼 양쪽에 보이는 상태는 T/framework-map의 GameMode/GameState/PlayerState/Actor/PlayerController 경계를 읽어 설계하고, host의 C++ OnRep 처리와 client 결과를 분리해 확인한다. 실제 네트워크 구현 방식 자체는 이 보고서에서 채택하지 않는다.

## 8. 이관 순서와 완료 확인 기준

### 8.1 추천 순서

1. **형식·경로를 먼저 고정한다.** Doc/기획.md 원본 보존, brief 신규 경로, 운영 기록 root, Story field/section·Requirement 형식·증거 필드를 정한다. project.yaml 네 값과 여섯 파생값은 유지한다.
2. **M의 정적 의존성을 준비한다.** 설정 helper·engine table·Story/체크포인트/증거 helper와 template을 먼저 대상용으로 검토한다. API reference·technical-preferences 덮어쓰기나 설정파일 통복사는 하지 않는다.
3. **세 스킬의 본문·계약을 함께 수정한다.** Unreal/PowerShell·예정 test_root·Doc 입력·2인 evidence·commit 별도 선택을 반영한다. 활성화하지 않은 reference·agent preload·allowed-tools 호출은 남겨두지 않는다.
4. **한 개의 작은 실제 기능을 파일 계약으로 대조한다.** Story 초안→In Progress→실제 Files→Run result/2인 결과→Completion Notes→다음 Story가 같은 ID·경로를 읽는지 확인한다. 이 단계의 engine 실행은 별도의 향후 구현 작업에서 수행한다.
5. **필요한 선택 기능만 추가한다.** 먼저 code-review/smoke-check, 필요 시 readiness·진행 조회·bug/playtest 기록. 자동 테스트가 필요해지면 test-setup의 로컬 UE 부분만 선택한다. CI·스프린트·team-qa·stage gate는 운영 필요가 생길 때 재검토한다.

### 8.2 이관 완료 기준

- [ ] 선택한 모든 SKILL의 직접 참조와 실제 활성 분기 의존성이 존재하거나, 미도입 안내로 명시적으로 정리되어 있다.
- [ ] 이 보고서의 61파일 판정과 실제 복사 목록이 일치하며 제외가 원본 삭제로 실행되지 않았다.
- [ ] target CLAUDE.md·project.yaml·framework-map·Doc/기획.md를 원본 템플릿으로 덮어쓰지 않았다.
- [ ] minimal에서 전체 GDD/TR/ADR/스프린트/QA팀·단계 사다리를 필수로 요구하지 않는다. 실제로 인용한 결정은 무시하지 않는다.
- [ ] 같은 Story의 상태·Files·Requirement·체크포인트·EPIC 표·Features tick이 일치한다. sprint YAML은 선택 도입한 경우만 검증한다.
- [ ] Story 생성 5–10 AC 권고, readiness의 coarse 최소 3/2/1 lint, QA 면제를 서로 다른 기준으로 해석한다.
- [ ] 예정 test_root와 code root 부재를 구분하며 미존재 경로를 묵시적으로 대체하거나 설정을 바꾸지 않는다.
- [ ] build/Automation/boot/화면/2인/Steam 결과가 분리되며 zero-test·미실행·과거 로그가 PASS로 집계되지 않는다.
- [ ] 커밋·stage 쓰기·출시 merge/tag/deploy가 Story 완료에 숨은 부작용으로 들어가지 않는다.
- [ ] 원본 Claude Code의 주입·Agent·hook·허용 도구 문법을 실제 사용할 호스트에서 확인했다. 파일 복사만으로 Codex에도 동일 동작한다고 선언하지 않는다.

### 8.3 미확정 사항

| 항목 | 현재 상태·필요한 결정 |
|---|---|
| brief/운영 문서 경로 채택 | Doc/game-brief.md는 신규 제안. source의 design/game-brief.md와 전 소비자 변경 목록을 함께 확정해야 함 |
| 첫 구현 Story | 세부 구현/우선 feature는 미확정. 기존 기획은 있으나 시스템별 상세 설계·실제 구현 성공은 확인하지 않음 |
| Automation 도입 시점 | 현재 QA minimal·예정 test_root. 자동 테스트를 필수화하거나 qa.level을 올리자는 결정은 없음 |
| smoke의 증거 저장 방식 | 원본 smoke-<date>.md는 하루 여러 실행의 덮어쓰기 위험. 실행 시각/Story 또는 run ID를 파일명에 추가할지 소비자 glob과 함께 검토 |
| 2인 실행 환경 | PIE/별도 프로세스/LAN/Steam·장비·계정 구성과 실제 접속 방식 미확정. commands.run만으로 재현되지 않음 |
| Steam 세션 API | framework-map §자주 나올 판단에 따라 OnlineSubsystem+Steam 또는 Online Services 선택을 별도 결정 |
| 버전 자료의 보완 | 원본 reference를 그대로 권위화하지 않고 필요한 API별 5.8.4 설치 소스 확인 범위 결정 |
| 커밋 기능 | 명시 요청 후 별도 수동 단계 권장. 원본 Commit this story 메뉴 유지 여부는 향후 이관 선택 |
| 향후 full 분기 보존 범위 | 삭제할 분기와 보존할 조건부 reference·role·template의 정확한 복사 명세는 실제 이관 때 고정 |

## 9. 주요 근거 파일

행 번호는 원본의 실제 파일을 대조한 위치이며 링크는 탐색용이다.

- [create-stories/SKILL.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/skills/create-stories/SKILL.md:89), [CONTRACT.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/skills/create-stories/CONTRACT.md:19)
- [dev-story/SKILL.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/skills/dev-story/SKILL.md:67), [CONTRACT.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/skills/dev-story/CONTRACT.md:66)
- [story-done/SKILL.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/skills/story-done/SKILL.md:74), [CONTRACT.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/skills/story-done/CONTRACT.md:78)
- [smoke-check/references/2-run-tests.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/skills/smoke-check/references/2-run-tests.md:105), [test-setup/references/3-directories.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/skills/test-setup/references/3-directories.md:205)
- [story template](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/docs/templates/story.md:1), [run-and-observe.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/docs/run-and-observe.md:174)
- [yaml-helper.sh](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/hooks/yaml-helper.sh:1443), [story-facts.sh](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/scripts/story-facts.sh:14), [test-run.sh](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/scripts/test-run.sh:280)
- [대상 CLAUDE.md](C:/Users/Sangha_Remote/Documents/TrackBuilding2/CLAUDE.md:1), [project.yaml](C:/Users/Sangha_Remote/Documents/TrackBuilding2/project.yaml:6), [기획.md](C:/Users/Sangha_Remote/Documents/TrackBuilding2/Doc/기획.md:138), [unreal-framework-map.md](C:/Users/Sangha_Remote/Documents/TrackBuilding2/.claude/docs/unreal-framework-map.md:8)

이번 보고서의 “완료”는 읽기·분석·보고서 저장의 완료다. 위 이관·구현·실행 확인 체크리스트의 완료를 뜻하지 않는다.
