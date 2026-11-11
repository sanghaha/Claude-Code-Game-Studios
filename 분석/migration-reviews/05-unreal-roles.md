# 05. Unreal 전문 역할 선택 이관 검토

작성일: 2026-11-11 (Asia/Seoul)  
상태: **분석·추천안. 역할 설치와 설정 변경은 미실행.**

## 1. 결론과 범위

**첫 이관 후보는 6개 역할이다.** `/dev-story`의 현재 분기를 유지하는 `engine-programmer`, `gameplay-programmer`, `network-programmer`, `ui-programmer`와 기본 엔진 검토자 `unreal-specialist`, 이 게임의 핵심 위험을 다루는 `ue-replication-specialist`를 권한다. 앞의 5개가 일반적인 코드 스토리 분기를 지원하는 묶음이고, 복제 역할 1개는 리슨 서버 게임에 맞춘 추가 추천이다. 매 스토리마다 6개를 실행한다는 뜻은 아니다.

- **지금 추천 6개:** 위 역할. 실제 이관 시 §5의 역할·호출자 수정과 함께 준비한다.
- **조건부 26개:** Blueprint·UMG, QA·리뷰·설계·콘텐츠·출시 등 해당 업무를 채택할 때 추가한다. `ue-blueprint-specialist`, `ue-umg-specialist`는 가까운 후속 후보지만 현재 최소 묶음의 필수 파일은 아니다.
- **현재 제외 17개:** 다른 엔진 역할 10개, 미사용 GAS, 분석·라이브 운영·커뮤니티·서사 역할 6개. **제외는 이관 목록에서 빼는 판단이며 원본 삭제가 아니다.** 이후 범위가 달라지면 재검토한다.

현재 확정된 프로젝트 전제는 Unreal 5.8.4, C++ 기반+Blueprint, Steam PC, 호스트 1명+클라이언트 1명의 리슨 서버다. `project.stage=Concept`, `project.goal=hobby`, `modes.rigor=minimal`, `modes.automation=collaborative`를 유지한다. 파생값 workflow=minimal, density=terse, QA=minimal, story_granularity=coarse, review=solo, team=individual도 바꾸지 않는다. 본문의 역할 선택과 변경안은 확정 결정이 아니다.

실행 범위는 원본·대상 읽기, 정적 호출 분석, 이 보고서 저장뿐이다. 원본 `SKILL.md`는 실행하지 않고 분석 자료로 읽었다. 역할·스킬·규칙·훅을 복사하거나 엔진 실행·빌드·게임 테스트·커밋을 하지 않았다. 프로젝트 공통 지침의 재작성과 다른 분석 보고서·색인 변경도 하지 않았다.

## 2. 확인한 기준과 현재 상태

### 2.1 경로와 지침

이하 `S`는 [CCGS 원본](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios), `T`는 [TrackBuilding2 대상](C:/Users/Sangha_Remote/Documents/TrackBuilding2), `E`는 [설치 엔진](<C:/Program Files/Epic Games/UE_5.8>)이다. `S/…`, `T/…`, `E/…` 표기는 이 절대 경로에 상대 경로를 붙인 것이다. 파일별 판정표의 역할 링크는 모두 실제 원본 파일이다. 행 번호는 이번에 읽은 파일 기준이다.

- `S`의 HEAD는 요청한 `abc0de79d6ceb0b5d7aa693945849b3937afbe6a`와 일치한다. 원본 `project.yaml`의 framework.version은 1.3.0이다.
- 두 저장소를 hidden/no-ignore 포함으로 검색했고, 각 저장소부터 `C:/`까지 상위 위치도 확인했으나 적용할 `AGENTS.md`를 발견하지 못했다. 현재 세션 지시와 대상 `CLAUDE.md`를 기준으로 삼았다.
- `S/CLAUDE.md`의 Collaboration Protocol과 `.claude/docs/coordination-rules.md`를 확인했다. 원본의 계층 위임 문구를 이번 분석에서 실행 지시로 사용하지 않았다. 지정 보고서 저장은 이미 승인된 작업이다.
- 대상 [CLAUDE.md](C:/Users/Sangha_Remote/Documents/TrackBuilding2/CLAUDE.md:3) 3–20, 24–45행과 [project.yaml](C:/Users/Sangha_Remote/Documents/TrackBuilding2/project.yaml:6) 6–43행에서 게임·폴더·엔진·명령·검증 전제를 대조했다.
- 참고한 `S/ax-setup/ccgs-adoption-reference.md` §6·부록 B는 **기존 TrackBuilding** 판단을 포함한다. 이를 TrackBuilding2의 결정으로 승계하지 않았다. `T/ax-setup/ccgs-settings-skill-impact.md` §2–4 및 동반 `.json`은 실제 역할·호출자·해석기와 대조했다.

### 2.2 디스크에서 확인한 사실

| 항목 | 확인 결과와 의미 |
|---|---|
| 실제 프로젝트·모듈 | `T/TrackBuilding.uproject` 3, 8행은 EngineAssociation `5.8`, Runtime 모듈 `TrackBuilding`. 폴더명 `TrackBuilding2`로 타깃·API 매크로·테스트 이름을 추정하지 않는다. |
| 실제 타깃 | `T/Source/TrackBuilding.Target.cs`, `TrackBuildingEditor.Target.cs` 각 6–13행: `TrackBuilding`, `TrackBuildingEditor`, Unreal5_8 include order. |
| 코드 루트 | `T/Source/TrackBuilding/`. 확인된 파일은 `TrackBuilding.h`, `TrackBuilding.cpp`, `TrackBuilding.Build.cs`. 게임 기능 구현과 2인 검증 성공은 확인되지 않았다. |
| 현재 모듈 의존성 | `TrackBuilding.Build.cs` 11–21행: Core/CoreUObject/Engine/InputCore/EnhancedInput. Slate와 OnlineSubsystem은 주석 예시. GAS·UMG·CommonUI·Steam의 실제 구현 의존성 추가가 확인된 상태가 아니다. |
| 테스트 루트 | `project.yaml` 24–25행의 `Source/TrackBuilding/Tests`는 예정 경로이며 폴더가 없다. 최소 QA라고 이 경로를 바꾸거나 빈 폴더를 만들지 않았다. |
| 대상 역할 | 조사 시 `T/.claude/agents/`가 없다. 원본의 자동 추론된 역할 이름이 곧 설치된 역할이라는 뜻은 아니다. |
| 기술 문서 | `T/.claude/docs/unreal-framework-map.md`는 존재한다. 요청에 읽기 자료로 적힌 `T/.claude/docs/technical-preferences.md`는 **조사 시 부재**했다. 부재를 임시 상태로 기록하며 원본 플레이스홀더로 채우지 않는다. |
| CommonUI | `T/Config/DefaultGame.ini` 2–8행에 CommonUI 설정·콘솔변수가 있다. `TrackBuilding.uproject` 13–21행의 명시 플러그인은 ModelingToolsEditorMode뿐이다. ini 흔적만으로 CommonUI 활성·입력 라우팅·화면 구현 완료를 판정하지 않는다. |
| 설치 버전 | `E/Engine/Build/Build.version` 2–5행에서 5.8.4, CL 58902052를 직접 읽었다. 에디터는 실행하지 않았다. |

기획 근거는 [Doc/기획.md](C:/Users/Sangha_Remote/Documents/TrackBuilding2/Doc/기획.md:138) §6–7이다. 동시 획득·배치는 서버 기준 선착순(142행), 호스트 종료 시 게임 종료(148행), Steam 친구 초대/로비(149행), 트랙·캐릭터·기차·타이머·승패 동기화(150–155행)가 명시돼 있다. 호스트 마이그레이션·전용 서버·재접속 지원을 새 요구사항으로 만들지 않는다.

## 3. 실제 호출과 최소 묶음

### 3.1 설정과 실행 주체를 구별해야 하는 이유

`S/.claude/hooks/yaml-helper.sh` 1252행은 minimal의 여섯 파생값을 정의한다. 2094–2128행에서 `primary`는 `engine.name`으로 추론되며 Unreal은 `unreal-specialist`다. 대상처럼 specialists 블록과 legacy 기술 설정이 없으면 code/shader/ui는 unset, additional은 none이다. **`specialists.primary`를 새로 적을 필요가 없고, 이 해석기는 그것을 직접 설정 키로 읽지도 않는다.**

| 실제 소비자 | 현재 값에서 일어나는 일 | 필요한 역할·경계 |
|---|---|---|
| `S/.claude/skills/dev-story/SKILL.md` 130–153행 | Config/Data는 에이전트 없이 처리. Foundation은 engine, UI는 ui, Visual/Feel·일반 gameplay는 gameplay, AI/pathfinding은 ai, networking은 network 프로그래머를 고른다. 코드 스토리에는 code specialist, unset이면 primary를 더한다. | 일반 경로 지원에 프로그래머 4개+unreal. AI는 실제 AI/pathfinding 스토리에서만 추가. `team.size`·`review_mode`는 이 스킬의 bootstrap 키에 없다(11행). individual/solo를 이유로 이 호출을 제거하면 원본 동작이 바뀐다. |
| 같은 파일 155–206행 | 먼저 프로그래머에게 구현을 맡긴 뒤 엔진 검토를 맡긴다. 엔진 검토는 변경 파일의 API 사용을 확인하고 file/line/problem/fix를 반환하며 **수정하지 않는다**. | 구현자와 검토자의 입력·편집 권한 분리를 유지. 복제 세부 검토는 §5의 명시 연결이 필요하다. |
| 같은 파일 172–175, 222–226, 252–266행 | QA=minimal이면 Logic/Integration 테스트 파일 작성·존재 검사를 면제. 화면 관찰은 면제하지 않는다. | qa-tester를 모든 dev-story에 추가하지 않는다. 자동 테스트 면제와 실제 작동 확인은 다른 문제다. |
| `S/.claude/skills/code-review/SKILL.md` 55–72, 147–170행 | 명시 실행 시 primary **한 명만** fresh-context helper로 호출. code/shader/ui는 lens에 넘기는 관점이다. qa-tester와 두 번째 specialist 호출은 금지한다. 이 스킬은 review_mode를 조회하지 않는다. | 지금은 unreal만으로 실행 가능. UMG 역할 채택 시 그 파일의 관련 절을 읽는 관점이 될 수 있지만 별도 helper가 자동 추가되는 것은 아니다. |
| `S/.claude/skills/story-done/SKILL.md` 248–264행 | solo면 QL-TEST-COVERAGE/LP-CODE-REVIEW를 건너뛴다. Reviewed by는 `self, no independent review`. | qa-lead·lead-programmer는 현재 종료 경로의 필수가 아니다. §3.1의 명시 code-review까지 금지되는 것은 아니다. |
| `S/.claude/docs/director-gates.md` 98–111행 | solo는 director gate 전부 skip. workflow는 gate가 실행될 경우의 panel 폭을 정하는 별도 축이다. | 기술감독·프로듀서·각 감독을 모든 스토리의 선행 승인자로 두지 않는다. |
| `S/.claude/skills/architecture-review/references/5-engine.md` 53–60행 | 엔진 관점을 **현재 실행 주체가 직접 적용**, 에이전트를 spawn하지 않는다. | 역할 이름이 문서에 등장했다는 이유로 역할 설치 의존성을 만들지 않는다. |
| `S/.claude/skills/design-system/references/lens-specialists.md` 7–15, 17–48행 | 선택된 역할의 관점으로 초안을 검토하는 lens 목록이다. | 이 목록의 모든 역할을 실행·복사하는 경로로 해석하지 않는다. |

`code-review/references/lens-engine.md` 3–11행도 configured specialist의 관련 heading을 읽는다고 규정한다. **역할 파일 읽기, 독립 검토자 호출, 구현 위임, supervisor gate는 서로 다른 소비 방식**이다.

### 3.2 team-*의 individual 활성 집합

다음은 해당 team 스킬을 나중에 채택하고 실행했을 때의 동작이다. team-* 전체가 현재 필수라는 뜻은 아니다. 각 경로는 `S/.claude/skills/<이름>/SKILL.md`다.

| 스킬 | individual에서 실제 활성 역할 | 근거 |
|---|---|---|
| team-ui | ui-programmer + ux-designer. 엔진 UI specialist는 studio 활성 집합에 추가된다. 본문의 Phase 3 호출보다 이 활성 집합 제한을 먼저 적용한다. | 41–45, 205행 |
| team-qa | qa-tester. qa-lead는 pipeline 자체 phase gate에서만 호출한다. solo director gate 생략과는 별개다. | 31–49, 116–141, 179, 273행 |
| team-combat | gameplay-programmer. AI 업무가 표시될 때 ai-programmer를 escalate. 다른 역할은 별도 spawn하지 않는다. | 49–53행 |
| team-level | level-designer만 | 42–46행 |
| team-audio | sound-designer만 | 42–46행 |
| team-polish | performance-analyst + technical-artist. 프로그래머 최적화 항목은 이 run에서 구현하지 않고 dev-story에 전달한다. | 41–44, 144행 |
| team-release | release-manager만 | 61–65행 |
| team-live-ops | live-ops-designer + economy-designer. 현재 게임 범위에는 채택하지 않는다. | 42–46행 |
| team-narrative | writer. 명시적인 pillar 충돌 때 narrative-director. 현재 게임 범위에는 채택하지 않는다. | 45–49행 |

따라서 individual/solo를 역할 파일 49개에 일괄 적용하는 삭제 필터로 사용하면 안 된다. 반대로 team 본문의 Team Composition 목록만 보고 전원을 복사·호출해도 안 된다. 실험적 독립 세션 Agent Teams는 별도 opt-in 문서(`S/.claude/docs/agent-teams.md` 7–26행)이며 이 이관에 포함하지 않는다.

### 3.3 권장 최소 실행 계약

1. 구현 스토리가 정해지면 해당 프로그래머 **한 명**을 고른다. networking 스토리는 network-programmer, UI 스토리는 ui-programmer다. C++ 권한 판정과 복제 코드가 같은 기능 파일에 있으면 파일 소유자를 한 명 정하고 다른 역할은 검토 의견을 반환한다.
2. 구현 보고 뒤 unreal-specialist가 읽기 전용 엔진 검토를 수행한다. 리슨 서버 권한·RPC·공유 상태·접속 수명주기를 건드리면 replication 관련 검토 항목을 적용한다.
3. `ue-replication-specialist`를 독립 호출할 경우 호출자가 대상 파일과 질문을 정해 **읽기 전용으로 한 번** 실행한다. `/code-review` 안에서는 두 번째 helper를 추가하지 않고 unreal helper가 복제 역할 파일의 관련 절을 lens로 읽게 한다. 실제 복제 구현을 맡기는 경우에만 해당 구현 파일에 쓰기 권한을 준다.
4. 막힌 구현·검토·관찰 결과는 그대로 반환한다. 미실행 빌드·네트워크·BP compile을 완료로 바꾸지 않는다. QA 담당 역할 설치와 별개로 호스트·클라이언트 양쪽 관찰이 필요하다.

역할 수를 3개(unreal/replication/gameplay)로 더 줄이는 변형도 가능하나, 그러려면 engine/network/UI 스토리를 한 구현자로 합치는 dispatcher 변경과 역할 책임 재정의가 필요하다. **현재 추천은 이 변형이 아니라 원본의 실제 분기를 유지하는 6개 묶음**이다. 당장 수행할 스토리 하나만 준비한다면 그 프로그래머+unreal의 2개에 필요한 복제 검토만 더하면 된다.

## 4. `.claude/agents/` 실제 49개 파일별 판정

버전 근거 표시: **UE**=각 역할의 Version Awareness가 `docs/engine-reference/unreal/`를 참조, **공통 API**=Engine Version Safety가 `[engine]/VERSION.md`·modules를 참조, **미명시**=독립 버전 확인 절차가 없어 호출 brief와 §5의 보완이 필요, **타 엔진**=Unity/Godot 자체 API·참조 체계. 어느 표시도 설치 5.8.4에서 모든 지침을 검증했다는 뜻은 아니다.

### 4.1 지금 추천 — 6개

| 실제 역할 파일 | 파일 내부 근거·버전 경계 | 현재 최소 경로에서 필요한 이유 |
|---|---|---|
| [engine-programmer.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/engine-programmer.md:64) | 64–82행 core/resource/lifecycle/debug 기반; 84–92행 **공통 API**; 103–108행 gameplay·build 업무 제외 | dev-story Foundation의 직접 목적지. 게임 모듈 기반 코드 담당으로 제한하며 설치 엔진 소스 수정 권한을 뜻하지 않는다. |
| [gameplay-programmer.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/gameplay-programmer.md:64) | 64–80행 기능·상태·입력 구현; 82–97행 **공통 API**, minimal은 ADR 부재 허용; 114–115행 networking 금지·테스트 강제 | 트랙 획득·배치·기차·라운드 로직 구현. networking 경계와 minimal 테스트 면제를 호출자와 맞춰야 한다. |
| [network-programmer.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/network-programmer.md:64) | 66–81행 프로토콜·복제·세션; 85–92행 권한·예측·마이그레이션. **미명시** | dev-story networking 직접 목적지, Steam 초대/로비·연결 수명주기의 담당자. UE 복제 전문 역할과 중복되는 범위를 §5처럼 정리한다. |
| [ui-programmer.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/ui-programmer.md:65) | 65–79행 화면/HUD/data binding; 81–95행 **공통 API**·engine 설정 확인; 107–113행 UX 설계·상태 직접 변경 금지 | dev-story UI의 직접 목적지. 타이머·승패·배치 경고 표시 구현을 맡을 수 있다. 화면 흐름의 미정 사항은 brief에 남긴다. |
| [unreal-specialist.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/unreal-specialist.md:61) | 61–94행 C++/BP·수명주기·binary 자산; 134–157행 **UE**; 159–201행 위임·직접 구현 금지 | engine.name으로 추론되는 기본 검토자. 엔진 사실 확인의 중심 역할. GAS 일괄 강제·고정 4개 위임 목록을 축소해야 한다. |
| [ue-replication-specialist.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/ue-replication-specialist.md:61) | 61–141행 권한·복제·RPC·prediction·relevancy; 143–159행 **UE**, networking.md; 161–166행 협업 | 2인 동시 조작·리슨 서버 호스트 표시·공유 상태의 검토 위험이 커 지금 추천. 이름을 additional에 넣는 것만으로 호출되지는 않는다. |

### 4.2 조건부 — 26개

| 실제 역할 파일 | 판정 근거·버전 경계 | 추가 조건과 현재 최소 경로에서의 필요성 |
|---|---|---|
| [accessibility-specialist.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/accessibility-specialist.md:56) | §Core Responsibilities·§Findings Format, 142–154행 미확인 기준 처리. **미명시** | 접근성 목표·대상 화면을 정해 audit할 때. 현재 전담 역할 필수 아님. team-ui individual에는 별도 활성 아님. |
| [ai-programmer.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/ai-programmer.md:64) | 64–85행 BT/pathfinding/perception·2ms 고정 예산. **미명시** | 실제 AI/pathfinding 스토리가 생길 때 dev-story가 직접 선택. 기차가 연결 트랙을 따라가는 것만으로 NPC AI가 확정되지는 않으며, 생성 경로 탐색을 AI 스토리로 분류하면 필요. |
| [art-director.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/art-director.md:70) | 70–94행 art bible·시각 기준; 96–117행 gate; 119–129행 코드·자산 제작 제외. **미명시** | 실제 시각 스타일·아트 규격을 분리 설계할 때. solo 감독 gate 때문에 미리 설치하지 않음. |
| [audio-director.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/audio-director.md:72) | 72–107행 사운드 정체성·event/mix·실제 음원 제작 제외. **미명시** | 사운드 팔레트·믹스 방향을 별도로 설계할 때. team-audio individual은 sound-designer만 활성. |
| [creative-director.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/creative-director.md:167) | 167–189행 비전·pillar·scope, 303–329행 코드/기술 금지와 gate. **미명시** | 명시적인 게임 정체성·부문 간 방향 충돌을 검토할 때. 현재 solo 기본 감독 승인 없음. |
| [devops-engineer.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/devops-engineer.md:64) | 64–81행 빌드·CI·artifact; 83–99행 브랜치 기본; 101–109행 게임 코드 제외. **미명시** | CI·패키징·Steam 배포 작업을 맡길 때. 기존 project.yaml commands 사용만을 위해 추가할 필요 없음. |
| [economy-designer.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/economy-designer.md:72) | 72–91행 registry, 93–138행 확률/자원 흐름·보상 수치. **미명시** | 자원 경제·보상 분포를 별도로 설계할 때. 현재 컨베이어 가중치 하나 때문에 장기 경제·상점·수익화를 도입하지 않음. |
| [game-designer.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/game-designer.md:74) | 74–101행 기획·edge cases, 195–220행 minimal brief; 8행 balance-check preload. **미명시** | 규칙을 상세화·변경할 때. 현재 기획의 소유권을 대체하지 않고 `Doc/기획.md`를 읽게 수정. 개발 역할을 쓰기 위한 supervisor 필수 아님. |
| [lead-programmer.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/lead-programmer.md:67) | 67–95행 API/코드 리뷰·고정 스타일; 84행 gate 형식, 7–8행 tech-debt·memory. **미명시** | 코드 경계 설계·별도 LP 리뷰를 선택할 때. solo story-done은 호출하지 않고 code-review helper도 Unreal primary다. |
| [level-designer.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/level-designer.md:72) | 72–104행 레벨 배치·페이싱·문서 검토, 110행 엔진 구현 금지. **미명시** | 장애물·웨이포인트·플레이 공간의 설계 검토를 분리할 때. 절차 생성 구현자는 gameplay/필요 시 AI이며 이 역할은 설계 산출물 담당. |
| [localization-lead.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/localization-lead.md:60) | 60–106행 i18n·추출/번역 흐름. **미명시** | 지원 언어·번역 작업을 정한 뒤 추가. Unreal FText/Localization Dashboard에 맞추며 JSON locale 체계를 강제하지 않음. |
| [performance-analyst.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/performance-analyst.md:59) | 59–75행 측정·예산, 101–115행 직접 최적화·추측 금지. **미명시** | 프로파일·성능 목표가 생길 때. 데이터 없이 수치·병목을 만들어내지 않음. team-polish를 채택하면 individual에도 활성. |
| [producer.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/producer.md:72) | §Key Responsibilities·§Sprint Planning Rules; 108–129행 gate, 8행 scope-check/estimate. **미명시** | 스프린트·일정·부문 조정이 필요할 때. hobby/minimal의 개발 시작 선행조건 아님. |
| [prototyper.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/prototyper.md:16) | 16–48행 concept/spike/slice, 71–102행 worktree, 119–129행 engine path, 185–224행 격리·보고서. **공통 API 성격의 engine 경로 확인** | Concept에서 가설 하나를 실험할 때 유용하나 production dev-story와 별개. 미커밋 project.yaml 등이 worktree에서 빠지는 문제와 실제 Unreal 모듈 편입 방법을 먼저 해결. |
| [qa-lead.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/qa-lead.md:63) | 63–106행 증거 분류·minimal 면제, 122–128행 gate, 7–8행 bug-report/release-checklist·memory. **미명시** | QA 전략·team-qa 자체 gate·출시 판정을 맡길 때. solo의 QL director gate는 skip이나 team-qa phase gate 담당은 남음. |
| [qa-tester.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/qa-tester.md:119) | 119–156행 Unreal Automation 예시, 190–218행 테스트 형식, 244–259행 bug-report 계약. **UE 예시는 있으나 독립 버전 확인 절차 미명시** | 테스트 작성·2인 수동 시나리오 정리를 위임할 때. 현재 QA=minimal에서 자동 테스트 담당을 상시 호출할 필요 없음. 경로·면제·증거를 수정해야 함. |
| [release-manager.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/release-manager.md:60) | 60–69행 출시 흐름, §Platform Certification Requirements·§Store Page Management, 7행 release 관련 3개 skill. **미명시** | Steam 출시·스토어 빌드 검증을 실제로 시작할 때. 현재 Concept의 최소 경로에서 불필요. |
| [security-engineer.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/security-engineer.md:61) | 61–94행 보안·입력 검증/TLS/세이브 암호화, 124–143행 findings. **미명시** | 세션 인증·외부 입력·저장/계정 경계를 실제 audit할 때. 서버 검증은 지금 구현 계약에도 포함하되 보안 전체 조직·TLS 일괄 요구는 자동 도입하지 않음. |
| [sound-designer.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/sound-designer.md:59) | 59–85행 SFX spec·event list·mix, 실제 음원/엔진 코드 제외. **미명시** | 스냅·오류·기차 상태의 사운드 명세를 맡길 때. 음원 파일 생성·추출 기능과는 다른 역할. |
| [systems-designer.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/systems-designer.md:72) | 72–112행 registry·변수표/범위/계산 예, 114–140행 상세 규칙·구현 금지. **미명시** | 스냅 허용 범위·가중치·난이도/생성 규칙을 수식화할 때. 엔진 상태 소유권 결정과 분리. |
| [technical-artist.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/technical-artist.md:64) | 64–81행 shader/VFX/pipeline; 83–91행 **공통 API**; 93–106행 프로젝트 예산 우선 | 머티리얼·Niagara·시각 피드백·렌더 측정을 맡길 때. Unreal 전용 shader 역할이 별도로 없다고 Unity shader 역할을 가져오지 않음. |
| [technical-director.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/technical-director.md:71) | 71–87행 기술 결정·ADR, 108–140행 gate/Accepted 권한. **미명시** | 엔진 업그레이드·새 플러그인·주요 경계 결정의 선택적 자문. 모든 시스템 ADR 강제를 minimal에 맞춰 조정. 현재 전체 감독 조직의 필수 아님. |
| [tools-programmer.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/tools-programmer.md:64) | 64–76행 editor/tooling, 78–90행 **공통 API**, 99–106행 runtime 변경·중복 도구 금지 | 반복 에셋 배치/검증 등 별도 도구 요구가 생길 때. 엔진 기본 도구를 먼저 활용. Editor 전용 의존성을 Runtime 모듈로 섞지 않음. |
| [ue-blueprint-specialist.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/ue-blueprint-specialist.md:62) | 62–104행 BP/C++ 경계·binary 에디터 작업; 109–110행 20-node 규칙; 161–177행 **UE** | BP 그래프 설계·검토를 별도로 맡길 때 추가. 현재 C++ 기반에는 specialists.code로 설정하지 않는다. 기본 unreal이 경계 판단을 담당할 수 있음. |
| [ue-umg-specialist.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/ue-umg-specialist.md:61) | 61–99행 UI 계층/바인딩/CommonUI, 114–128행 입력·성능, 147–163행 **UE** | 복잡한 UMG·입력 포커스·위젯 수명 검토 시 추가. 먼저 일반 ui-programmer+unreal로 시작 가능. CommonUI·gamepad·2ms 예산은 자동 확정하지 않음. |
| [ux-designer.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/ux-designer.md:65) | 65–95행 흐름·입력·접근성; 97–102행 구현/미술 결정 제외. **미명시** | 초대/접속·로비·준비·실패 흐름이나 미정 HUD 인터랙션을 설계할 때. team-ui 채택 시 individual에도 필요. |

### 4.3 현재 제외 — 17개

| 실제 역할 파일 | 실제 파일의 범위·근거 | 이관에서 제외하는 이유 |
|---|---|---|
| [analytics-engineer.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/analytics-engineer.md:58) | 58–79행 telemetry/funnel/A-B/dashboard/privacy. **미명시** | hobby Concept의 현재 게임 요구에 계측 수집·운영 분석 체계가 없다. 프로파일링·로컬 디버그 로그와 구별. |
| [community-manager.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/community-manager.md:56) | 56–62행 공지·피드백·커뮤니티; 148–162행 운영 문서. **미명시** | 플레이어 커뮤니티 운영 업무를 현재 이관 범위로 채택하지 않음. |
| [godot-csharp-specialist.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/godot-csharp-specialist.md:61) | 8, 61–68행 Godot .NET·Signal·csproj. **타 엔진** | Unreal C++/BP와 다름. |
| [godot-gdextension-specialist.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/godot-gdextension-specialist.md:61) | 8, 61–67행 godot-cpp/rust·GDExtension·SCons. **타 엔진** | C++를 쓴다는 공통점만으로 Unreal 네이티브 역할로 사용하지 않음. |
| [godot-gdscript-specialist.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/godot-gdscript-specialist.md:63) | 8, 63–69행 GDScript·signal·coroutine. **타 엔진** | 사용 언어·노드/에셋 체계 불일치. |
| [godot-shader-specialist.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/godot-shader-specialist.md:61) | 8, 61–67행 gdshader·Forward+/WorldEnvironment. **타 엔진** | Unreal 머티리얼·USF·Niagara용 역할로 재사용하지 않음. |
| [godot-specialist.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/godot-specialist.md:61) | 8, 61–67행 Godot 4·node/scene/autoload/export. **타 엔진** | engine.name=Unreal. 하위 Godot 역할 연결도 불필요. |
| [live-ops-designer.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/live-ops-designer.md:61) | 61–90행 시즌·battle pass·retention; 153–159행 live-ops 문서. **미명시** | 현재 2인 협동 게임 기획에 시즌·일일 보상·라이브 서비스가 없음. |
| [narrative-director.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/narrative-director.md:72) | 72–90행 서사/캐릭터/대화 구조; 117–126행 writer/world-builder. **미명시** | 현재 기획에서 별도 서사 제작·정사 충돌 작업이 정의되지 않음. |
| [ue-gas-specialist.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/ue-gas-specialist.md:61) | 61–68행 GAS abilities/effects/attributes; 131–147행 **UE**, GAS plugin 문서 | 현재 트랙 빌딩 규칙은 GAS 사용을 요구하지 않음. Build.cs에도 GameplayAbilities 의존성이 확인되지 않음. 태그 사용·멀티플레이 자체가 GAS 채택 근거는 아님. 채택 결정 시 재검토. |
| [unity-addressables-specialist.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/unity-addressables-specialist.md:61) | 8, 61–67행 Addressables/catalog/bundle. **타 엔진** | Unreal soft reference/Asset Manager와 API·pipeline이 다름. |
| [unity-dots-specialist.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/unity-dots-specialist.md:61) | 8, 61–67행 ECS/Jobs/Burst. **타 엔진** | Unreal C++·Actor 기반 프로젝트에서 사용하지 않음. |
| [unity-shader-specialist.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/unity-shader-specialist.md:61) | 8, 61–67행 Shader Graph/VFX Graph/URP/HDRP. **타 엔진** | HLSL 공통점이 엔진 전용 역할의 호환성을 만들지 않음. |
| [unity-specialist.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/unity-specialist.md:61) | 8, 61–67행 MonoBehaviour/DOTS/package/build. **타 엔진** | engine.name=Unreal. Unity 하위 위임 트리도 제외. |
| [unity-ui-specialist.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/unity-ui-specialist.md:61) | 8, 61–67행 UI Toolkit/UGUI/UXML/USS. **타 엔진** | UMG/CommonUI와 다른 프레임워크. |
| [world-builder.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/world-builder.md:72) | 72–105행 정사·역사·문화·세계 규칙. **미명시** | 랜덤 장애물 맵 생성과 lore world-building은 다른 업무. 현재 필요 없음. |
| [writer.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/writer.md:77) | 77–111행 대사·lore·문구; 113–119행 코드/시스템 설계 제외. **미명시** | 현재 서사·대사 제작 단위가 없음. 짧은 UI 오류 문구만을 위해 서사 위임 체계를 들여오지 않음. |

## 5. Unreal용으로 바꿀 위치·문제·변경안·소비자 영향

이 절은 **나중에 역할을 이관할 때의 편집 명세**다. 아래 원본 또는 대상 파일을 이번에 수정한 것이 아니다.

### 5.1 버전·문서·모듈 경로

| 원본 위치 | 현재 문제 | 변경안 | 함께 고칠 소비자·영향 |
|---|---|---|---|
| unreal 134–150, replication 143–159, blueprint 161–177, umg 147–163, gas 131–147행; 일반 구현자 Engine Version Safety 절 | `docs/engine-reference/...`를 필수 입력으로 가정. 대상에는 이 트리가 없고 원본 VERSION은 설치판을 `NOT DETERMINED`, latest hotfix를 5.8.3으로 기록(6–9행). 일부 reference 본문은 5.7 때 작성. | 권장 경로는 `project.yaml` → 설치 `Engine/Build/Build.version` → 기존 `.claude/docs/unreal-framework-map.md` → 필요한 설치 소스다. 버전·API 근거에 파일/심볼/행·검증일을 붙인다. 읽지 못한 API는 확인 필요. 원본 문서는 보조 자료로만 선별 사용. | dev-story 202–206행, code-review lens 9–11행, architecture review lens의 reference 강제도 같은 우선순위로 바꿔야 한다. 역할만 바꾸면 호출 brief가 계속 부재 문서를 요구한다. |
| unreal 23–26행 등 공통 Implementation Workflow 예시 | `static utility or scene node`라는 타 엔진식 질문이 반복되고 기본 경로가 `src/`, `tests/` 또는 generic design/docs다. | Actor/Component/Subsystem/순수 C++ 타입, 수치/상태, authority/로컬 표시를 구분하는 질문으로 교체. 코드 root는 `Source/TrackBuilding`, 프로젝트·타깃명은 TrackBuilding. 테스트 root는 설정대로 예정 경로임을 명시. | programmer brief, code-root-resolution, story Test Evidence, QA 역할의 경로가 일치해야 한다. TrackBuilding2라는 모듈을 새로 만들지 않는다. |
| gameplay 92, game-designer 99–101/195–201, GAS 103행 등 | `design/game-brief.md`, `design/gdd/`, `docs/architecture/`와 registry를 고정 가정한다. | 현재 기획은 `Doc/기획.md`의 필요한 절. 아직 채택하지 않은 ADR·registry·작업 문서는 입력 없음으로 전달. 새 문서 경로는 그 단위 이관에서 결정하고 reader/writer를 함께 변경. | dev-story Phase 1/2/5, QA·story-done·review의 문서 탐색도 동일 경로를 사용. 이 보고서가 새 설계 문서 경로를 확정하지 않는다. |
| engine-programmer 66–82, tools 66–74/99–106, unreal 66행 | engine programmer가 설치 엔진이나 새 framework까지 만들어야 한다고 확장 해석하기 쉽다. | 기본 작업은 게임 Runtime 모듈 안이다. 설치 엔진 소스는 **읽기 근거**다. Build.cs·타깃·Config·uproject·Editor module·plugin 변경은 명시 scope로 분리. 엔진 제공 기능과 새 도구의 중복을 먼저 확인. | dev-story 186–188행의 code-root 제한 때문에 Build.cs 외부 타깃/Config/Content 작업은 경계와 목적 경로를 brief에 따로 명시해야 한다. |

기존 `CLAUDE.md`, `project.yaml`, `Doc/기획.md`, `.claude/docs/unreal-framework-map.md`는 원본 템플릿으로 덮어쓰지 않는다. 없는 technical-preferences를 만들어야 역할이 작동하는 것도 아니다. **전체 설정파일 복사는 권하지 않는다.** 향후 specialists 키를 추가할 때도 채택한 키만 병합하고 기존 네 설정·여섯 파생 정책·엔진 경로·명령은 유지한다.

### 5.2 권한·RPC·복제·리슨 서버 표시

| 원본 위치 | 현재 문제 | Unreal 5.8.4/2인 리슨 서버 변경안 | 소비자 영향 |
|---|---|---|---|
| network-programmer 66–92행; replication 62–68/85–102행; network-code rule 10–17행 | 일반 네트워크 프로토콜·rollback·prediction·reconnect/host migration을 기본 요구처럼 읽을 수 있음. transport와 Unreal 복제 역할도 겹침. | network는 Steam 세션/초대·join/leave·travel/종료·실패 처리와 게임 모듈 통합을 담당. replication은 UE 권한/RPC/replicated state·relevancy를 담당. 실제 코드 소유자는 하나. 엔진 transport·serialization을 다시 만들지 않는다. 호스트 종료=종료를 지키고 reconnect/migration은 별도 결정 전까지 scope 밖. 이동은 기존 movement 체계를 먼저 검토하며 트랙 배치 rollback을 자동 요구하지 않는다. | network 구현 brief, unreal 검토, replication lens, 향후 network rule을 같은 계약으로 맞춘다. 150ms·5/10KB/s는 원본 기본 제안치이며 대상 확정 예산으로 쓰지 않는다. |
| unreal 111–116, replication 72–94행 | RPC 방향과 property callback만 있고 GameMode/GameState/PlayerController의 존재·소유 연결을 놓치기 쉬움. | GameMode=서버 규칙 판정, GameState=공유 라운드/시간/결과, PlayerState=공유 플레이어 상태, Pawn/Actor=필요한 월드 상태. Subsystem은 로컬 서비스. 무소유 트랙 Actor로 직접 Server RPC를 보내지 않고 소유 PlayerController/Pawn/Component로 요청을 보낸다. | 게임/네트워크/복제/UI 역할 모두 framework map 10–28행을 읽도록 한다. 기존 클래스가 없으면 배치안이며 실제 구현 완료라고 쓰지 않는다. |
| replication 85–94, 125–132행 | 일반 validation만으로는 동시 획득·배치의 중복 확정 위험이 남음. Reliable multicast가 영속 state 대용이 될 위험도 있음. | 서버가 플레이어 소유/거리/소지 1개/조각 유효성/연결·충돌/기차가 지나간 상태를 확인한 뒤 한 경로에서 선착순으로 결과를 확정. 재요청·경합의 두 번째 요청은 결과를 다시 만들지 않음. 지속 상태는 replicated property/구조로, 일회 연출은 필요 시 RPC로 나눈다. | Doc/기획.md 82–99/138–155행에서 요구한 사례를 gameplay/network brief와 수동 검증 시나리오로 연결. 새 경합 정책·늦은 접속 허용 정책은 미정이면 질문으로 반환. |
| unreal 114행, replication 79–80행 | `ReplicatedUsing`을 사용하면 호스트 HUD도 자동 갱신된다고 오인할 수 있음. | **C++ OnRep는 수신 측 callback이라는 한계를 명시한다.** 권한 측 state setter와 클라이언트 OnRep가 공통의 상태 변경 통지/표시 갱신 함수를 통해 로컬 관찰자를 갱신하도록 설계. Host authority와 `IsLocalController` 여부를 별도로 판단한다. BP RepNotify의 세부 호출 특성은 C++와 동일하다고 추정하지 않고 사용 경로를 확인한다. | replication 검토와 ui/umg 이벤트 계약에 반영. 호스트·클라이언트 양쪽의 타이머/승패/조각 상태를 검증해야 함. |
| replication 81행 `Never replicate derived/computed values` | 예외 없는 규칙은 서버가 확정해야 하는 결과까지 클라이언트 계산으로 밀 수 있음. | 타이머 표시는 서버 종료시각과 서버 시간 추정으로 로컬 계산. 배치/경로 유효성·승패 같은 authoritative 결과는 서버가 확정하고 필요한 결과를 공유. 무엇을 재계산할지 결정 근거를 적는다. | framework map 62–67행과 gameplay/UI brief를 일치. 같은 random seed만 보내면 배치가 언제나 동일하다고 가정하지 않음. |
| replication 104–111행 | `NetRelevancyDistance`를 속성명처럼 제시, DormantPartial을 property change replication이라고 설명, NetUpdateFrequency를 actor tick rate라고 표현. | 설치 Actor.h의 `SetNetCullDistanceSquared`/`GetNetCullDistanceSquared`, `SetNetUpdateFrequency`를 확인해 사용. replication update와 gameplay Tick을 구분. DormantPartial은 연결별 dormancy이며 변경 감지 자동 전송이라는 뜻이 아니다. dormancy 사용 시 wake/flush와 대상 복제 backend를 별도 검토. 초기 최소 게임에 고급 dormancy·대역폭 최적화를 강제하지 않음. | replication 역할과 관련 networking reference에 같은 정정 필요. 5.8.4 근거는 §5.5. |
| 원본 VERSION 73행·networking.md 6–18행, 대상 framework map 53행 | 원본의 production-ready 설명, 플러그인 경로 `Experimental`, 실제 Beta flag, 기본 backend가 서로 다른 사실인데 한 상태로 오인하기 쉬움. | 설치 IrisConfig 기본값 0, Iris.uplugin EnabledByDefault=false/IsBetaVersion=true/IsExperimentalVersion=false를 각각 기록. 폴더명이 Experimental이라고 해당 flag가 true인 것은 아님. 현재 설정 검색에서 Iris 활성 override를 찾지 못했으나 런타임 backend는 미관찰. Iris 전환은 추천 묶음의 전제가 아니다. | 역할 이관으로 기존 맵을 통째로 교체하지 않음. 맵 53행의 “Experimental 플러그인” 표현 정정 필요성을 이 보고서에 전달한다. backend 전환 시 engine 근거를 다시 확인. |

### 5.3 Blueprint·UMG·GAS의 과잉 기본값 줄이기

| 원본 위치 | 현재 문제 | 변경안·영향 |
|---|---|---|
| unreal 96–101/164/196/207행, gameplay와 replication의 GAS 예시 | GAS를 사용하지 않는 게임에도 combat/stat/tag 체계를 끌어올 수 있음. | GAS 절·위임은 **GAS가 채택된 경우만** 적용. 현재 목록에서 ue-gas-specialist와 필수 GAS 문서를 제외. Gameplay Tags 단독 사용과 GAS 사용을 구분한다. 추후 채택 때 AbilitySystemComponent/플러그인/Build.cs/복제/QA를 한 묶음으로 검토. |
| unreal 82–94행; blueprint 70–104/109–110/143–159행 | binary 보호는 유용하지만 `settings deny`가 실제 설치 전부터 있다고 단정. BP 역할은 Bash·에디터 도구가 없다고 명시. 20-node 제한·모든 testable logic C++는 프로젝트 정책과 별도로 강제됨. | 자산은 editor/MCP/commandlet 등 **실제로 주어진 도구**로만 변경. 도구가 없으면 그래프·노드·핀·변수·compile 절차를 반환하고 미적용 표시. `.uasset/.umap` byte edit 금지 유지. 20개 node는 리팩터링 참고값으로 낮추고 프로젝트가 정한 기준 우선. BP는 에셋 구성·수치 조정, C++는 핵심 규칙·권한/RPC라는 현재 선택을 따른다. |
| umg 71–99/114–128행, ui 93–105행 | CommonUI를 모든 screen/button/input에 강제하고 gamepad·UI<2ms를 확정 예산처럼 둠. NativeTick refresh 권장(96행)과 event-driven 권장(98/124행)이 충돌. | UUserWidget/UMG를 기본 후보로 두고 CommonUI·MVVM·ViewModel 층은 실제 필요와 채택 여부에 따라 선택. CommonUI ini 흔적을 검토하되 활성 완료로 단정하지 않음. 이벤트 기반 상태 갱신, 초기 snapshot+delegate 연결/해제, travel 시 재바인딩·null 처리를 명시. 타이머의 로컬 시각 갱신 등 필요한 연속 표시는 상태 복제 빈도와 구분. |
| umg 66/93–95행, ui 112–113행 | 상태 표시와 변경 요청의 경계는 있지만 리슨 서버 로컬 UI와 소유 연결이 불명확. | UI는 로컬 플레이어가 만들고 복제된 읽기 모델을 표시한다. 버튼은 소유 Controller/Pawn의 요청 경로로 전달하며 GameMode를 클라이언트에서 읽지 않는다. 호스트도 로컬 UI를 가진다는 검토 항목을 추가. 남은 시간/구간/승패/접속 오류의 최초 값과 재연결·레벨 이동 시 표시를 확인하되 재접속 기능 자체를 새로 요구하지 않음. |
| umg 107–136행, ui 76–79/103행 | 다국어/RTL·gamepad·세 가지 theme·자막·화면읽기 등을 현재 범위에 자동 추가할 수 있음. | 현재 primary input은 Keyboard/Mouse다. FText·색만으로 정보 전달하지 않기·읽기 쉬운 표시 등 기본 설계는 유지하고, gamepad/지원 언어/접근성 tier/성능 수치의 범위는 별도 요구로 결정. UI 역할만 복사해 새로운 제품 요구를 확정하지 않음. |

### 5.4 최소 모드·승인·역할 경계

| 원본 위치 | 현재 문제 | 변경안·소비자 영향 |
|---|---|---|
| gameplay 79–80/115행; qa-tester 66–73/168–170/207–218행; qa-lead 67–73행 | 모든 gameplay unit test 강제·일반 tests 경로·기본 BLOCKING 표가 dev-story minimal 면제와 충돌. qa-lead 본문에는 면제가 있지만 표만 재사용하면 잘못 판정 가능. | brief의 resolved qa.level/gate_level와 story type을 우선. 현재 Logic/Integration 자동 테스트는 waived, UI/Visual 증거 및 실제 2인 관찰은 별도. 테스트 작성 업무를 명시적으로 선택하면 `Source/TrackBuilding/Tests`, `TrackBuilding.*` filter, WITH_DEV_AUTOMATION_TESTS 등을 실제 엔진·명령과 대조. 테스트 파일 부재를 성공/실행 실패로 바꾸지 않는다. |
| qa-tester 121–122행, evidence 표 211–215행 | `Source/<Module>/Private/Tests/`만 UBT가 컴파일한다고 단정하는 반면 대상 설정은 `Source/TrackBuilding/Tests`. generic `tests/unit` 표도 동시에 존재. | 하드코딩 대신 resolved test root를 사용. 대상 예정 경로는 module 내부이며 아직 생성·컴파일하지 않았다고 기록. 모듈 포함/컴파일 여부는 나중 실제 테스트 추가와 build에서 확인. 폴더를 원본에 맞추기 위해 지금 설정 변경하지 않음. |
| technical-director 73–75, lead 69–82/88–104, unreal 161–184행 | 모든 시스템 ADR·고정 감독 sign-off·pair review·고정 complexity/line 기준이 minimal/solo와 충돌할 수 있음. | 현재 tier가 필요로 하는 입력만 요구. routine class placement는 기존 framework map과 승인된 story 범위로 결정하고, unresolved 주요 선택은 사용자/호출자에게 선택지로 반환. director 역할이 설치되지 않았다는 이유만으로 정상 개발을 막지 않음. 기존 Accepted ADR이 있으면 준수. |
| unreal 187–201행, 각 역할 Delegation Map | 제외한 GAS·조건부 BP/UMG·미설치 director를 무조건 시작할 위험. Agent 도구가 없을 때 직접 수행하라는 193–194행과 직접 feature 구현 금지 183행도 충돌. | 설치·허용된 역할만 표에 남기고 미설치 역할은 호출자에게 질문/검토 관점으로 반환. 엔진 검토 중 하위 spawn은 기본 금지하고 호출자가 범위별로 선택. Agent 부재 시 동일한 읽기 검토를 수행할 수 있으나 구현 권한은 자동 확대하지 않음. |
| 구현자 공통 Collaboration Protocol, 예: unreal 16–50행; 각 도구 frontmatter | 원본은 파일마다 재승인·질문을 요구하는 문구를 중복 보유. 기존 승인 범위도 다시 묻거나 질문 도구 없는 역할이 직접 사용자 입력을 기다릴 위험. `model: sonnet/opus`, memory·worktree 설정도 원본 실행 환경 전제. | 대상의 이미 완료한 공통 지침과 현재 승인 범위를 참조하도록 역할 내부 문구를 정리. 역할은 미정 선택·범위 밖 변경을 결과로 반환하고 호출자가 사용자 대화를 담당. 원본 모델명을 대상 실행기의 모델 설정으로 무조건 통복사하지 않는다. review 호출은 Write/Edit/Bash 권한이 있어도 brief로 읽기 전용을 고정. |
| network rule paths 3–5행, engine/ui rule의 paths | `Source/**/Network` 같은 폴더 분류는 Track/Train/Conveyor 기능 폴더에 섞인 RPC를 놓칠 수 있음. | 역할의 검토 범위는 변경된 `Source/TrackBuilding/**/*.{h,cpp}`에서 RPC/UPROPERTY/authority 사용으로 고른다. 경로 규칙 이관 단위에도 영향 전달. 이 보고서에서는 공통 규칙을 재작성하지 않음. |
| prototyper 73–89행·185–206행 | worktree가 현재 미커밋 설정을 못 보고 commit을 선행 요구. Unreal은 별도 prototypes 아래 cpp만 만들면 게임 모듈에서 자동 빌드되지 않을 수 있음. | 채택할 때 작업트리의 필요한 입력 가시성과 독립 .uproject/모듈 편입 전략을 명시. 사용자에게 불필요한 commit을 강제하는 기본값을 그대로 이관하지 않음. production 소스와의 격리는 유지. 지금 prototype·worktree를 생성하지 않음. |

### 5.5 이번에 직접 대조한 설치 소스

다음은 인터넷의 최신 기능 소개를 복사한 것이 아니라 **설치된 5.8.4 파일에서 읽은 근거**다. runtime 실행 결과는 포함하지 않는다. 경로는 모두 `E/Engine/` 기준이다.

| 확인한 항목 | 설치 파일·행 |
|---|---|
| 버전·CL | `Build/Build.version` 2–5행 |
| 공유 GameState/PlayerState 기본 복제 | `Source/Runtime/Engine/Private/GameStateBase.cpp` 25–26행, `PlayerState.cpp` 25–26행 |
| Controller owner relevancy / Actor 복제 기본 꺼짐 | `Source/Runtime/Engine/Private/Controller.cpp` 67행, `Actor.cpp` 289행 |
| 소유 연결 없는 Actor RPC 처리 실패 | `Source/Runtime/Engine/Private/NetDriver.cpp` 8274행의 ProcessRemoteFunction 경고. 해당 actor/function이 처리되지 않는다고 명시. |
| C++ RepNotify 수신 상태 경로 | `Source/Runtime/Engine/Private/RepLayout.cpp` 4661행의 `CallRepNotifies(FReceivingRepState*, UObject*)`; 기존 대상 map 46행의 수신 측 설명과 대조. |
| 로컬 Controller 위젯 생성 검사 | `Source/Runtime/UMG/Private/UserWidget.cpp` 2736행 `IsLocalPlayerController` 검사 |
| relevancy/frequency API | `Source/Runtime/Engine/Classes/GameFramework/Actor.h` 898–910행 public field deprecation, 4624–4654행 Set/GetNetUpdateFrequency·Set/GetNetCullDistanceSquared. `NetRelevancyDistance`라는 설정 필드를 이 범위의 대체 API로 사용하지 않음. |
| DormantPartial의 실제 의미 | `Source/Runtime/Engine/Classes/Engine/EngineTypes.h` 3595–3608행: 일부 연결에 대해 dormancy를 결정하며 `GetNetDormancy()`로 확인. `Actor.h` 2849행도 연결별 판단임을 설명. |
| Iris 기본 선택과 metadata | `Source/Runtime/Net/Iris/Private/Iris/IrisConfig.cpp` 15–16행 기본 0; `Plugins/Experimental/Iris/Iris.uplugin` 13–16행 EnabledByDefault=false, IsBetaVersion=true, IsExperimentalVersion=false |

설치 소스의 일부를 확인한 사실과 원본 engine-reference 전체의 정확성을 검증한 일은 다르다. GAS·CommonUI·Iris·예측·플러그인 API 전체를 이번에 인증하지 않았다.

## 6. 함께 준비할 실제 의존성

### 6.1 최소 역할을 쓰는 데 필요한 입력·출력 계약

| 역할 | 입력 | 출력 | 쓰기·결정 경계 |
|---|---|---|---|
| engine-programmer | Foundation story, project.yaml, 실제 module/target, framework map, 필요한 installed source | 기반 C++ 변경·공개 API·선택 이유·미검증 항목 | 승인된 게임 코드. 엔진 설치본·CI·게임 규칙 변경은 scope 밖이면 반환. |
| gameplay-programmer | story AC, `Doc/기획.md` 해당 절, 상태/RPC 분담, 조정 수치 저장 방식 | 기능 코드·상태 전이·의도 밖 선택·검증 결과 | 게임 규칙을 새로 만들지 않음. 네트워크와 같은 파일을 쓸 때 단일 owner 계약 필요. |
| network-programmer | 2인/Steam/호스트 종료 요구, 세션 backend 선택, engine version·module | 연결/세션 구현·권한/RPC 통합 경계·실패 처리 | 새 backend·전용 서버·host migration·새 plugin 활성은 결정 필요. |
| ui-programmer | 화면 상태/흐름, 읽을 공유·로컬 state, request API, input 범위 | C++ UI 로직·에디터 적용 절차·표시/초기화/해제 계약 | UI가 게임 결과를 확정하지 않음. 실제 도구 없는 binary 편집 불가. |
| unreal-specialist | 변경 파일·engine/version·framework map·해당 source | file:line/problem/fix·근거·confidence·미확인 사항 | dev-story/code-review에서는 read-only. 역할 파일의 plugin/config 권한으로 확대하지 않음. |
| ue-replication-specialist | 권한/소유권 표, 변경 파일, 동시 입력/late join/travel 정책, backend | RPC 경로·복제 필드/범위·host 표시·경합 findings | review면 read-only, 구현이면 지정 파일만. network owner와 중복 편집하지 않음. |
| BP/UMG 추가 시 | C++ hooks, 에셋 경로, 그래프/위젯 상태, 실제 도구 능력 | 적용된 자산+compile 결과 또는 미적용 editor steps | blueprint 역할은 원본에 editor/Bash 권한이 없음. 필요한 tool 제공 여부를 먼저 판정. |
| QA/리뷰 추가 시 | resolved QA/gate, story AC, 실제 로그·스크린샷·양쪽 관찰 | 재현 절차·PASS/FAIL/NOT ASSESSED·파일 근거 | 테스트를 쓰는 일과 실행 성공, 사용자가 말했다는 결과와 직접 관찰을 분리. |

### 6.2 실제 파일 묶음과 조건

역할 Markdown 자체가 shell script를 실행해야만 읽히는 것은 아니다. 다음 의존성은 **그 역할을 부르는 CCGS 경로까지 채택할 때** 생긴다. 경로들은 실제 `S` 아래에서 확인했다. 원형 유지와 Unreal 전용으로 축소한 권장안을 구분한다.

| 채택 단위 | 함께 필요한 실제 파일 | 조건·최소화 판단 |
|---|---|---|
| 6개 역할의 공통 입력 | 대상 기존 `CLAUDE.md`, `project.yaml`, `.claude/docs/unreal-framework-map.md`, `Doc/기획.md`, `TrackBuilding.uproject`, `Source/TrackBuilding/TrackBuilding.Build.cs`, 두 Target.cs, 설치 Build.version/관련 source | **기존 입력 사용.** 대상 문서를 원본으로 덮어쓰거나 추가 technical-preferences로 복제하지 않음. |
| 원본 버전 지침을 그대로 보존할 경우 | `docs/engine-reference/unreal/VERSION.md`, `deprecated-apis.md`, `breaking-changes.md`, `current-best-practices.md`; replication→`modules/networking.md`, UMG→`modules/ui.md`·`plugins/common-ui.md`, BP→관련 modules, GAS→`plugins/gameplay-ability-system.md` | 역할의 hard reference 의존성. 권장안은 §5.1처럼 기존 map/설치 source로 우선순위를 고쳐 전체 트리 복사를 피한다. 원본 reference를 가져올 경우 검증 범위·설치판 차이를 표시하고 문서 내 링크를 끝까지 확인. |
| dev-story 경로 | `.claude/skills/dev-story/SKILL.md`, `CONTRACT.md`, `references/engine-checks.md`, `references/version-mismatch.md`; `.claude/hooks/yaml-helper.sh`; `.claude/scripts/story-facts.sh`, `adr-status.sh`, `md-section.sh`, `session-state.sh`, `review-receipts.sh` | CONTRACT는 실행 코드가 아닌 스킬 계약/유지보수 자료. version-mismatch는 스토리 stamp 불일치 때만 읽음. adr-status는 ADR이 실제 지정될 때. session-state는 성공·중단 모두 호출. screenshot stamp에는 review-receipts의 hash 경로가 쓰임. |
| 설정/루트 해석 | `.claude/hooks/yaml-helper.sh`, `.claude/scripts/engine-row.awk`, `.claude/engines/engines.tsv`; 참고 `.claude/docs/config-resolution.md`, `code-root-resolution.md` | helper 1433–1434행이 TSV/AWK 위치를 결합한다. Bash·Python fallback 및 awk/sed 등 실행 환경을 확인해야 함. 역할만 복사한다고 inline bootstrap이 작동하지 않음. 현재 원본 TSV의 기본 test root로 대상 값을 덮어쓰지 않음. |
| dev-story 문서 계약 | `.claude/docs/automation-modes.md`, `error-recovery-protocol.md`, `run-and-observe.md`, `code-root-resolution.md`; 선택한 story/checkpoint 형식을 쓰면 `.claude/docs/templates/story.md`, `session-state.md` | 현재 minimal은 TR registry·manifest·ADR을 무조건 요구하지 않음. 문서 경로·checkpoint 정책은 해당 이관 단위와 함께 조정. 공통 지침 재작성 작업은 이 보고서 범위 밖. |
| 명시 code-review | `.claude/skills/code-review/SKILL.md`, `references/lens-engine.md`, 설정 reader, unreal 역할 | 원본 Phase 7 helper-result/1 계약(status/conclusion/evidence/actions_taken/confidence/caveats) 보존. 전체 팀·qa-tester·lead-programmer는 필수 아님. review-receipts를 이 스킬이 직접 쓰는 것으로 오인하지 않음. |
| story-done의 선택 review gate | `.claude/skills/story-done/references/review-gates.md`, `.claude/docs/director-gates.md`, `director-gates/ql-test-coverage.md`, `director-gates/lp-code-review.md`, qa-lead/lead 역할 | 현재 solo는 이 reference도 로드하지 않음. full override 등을 명시 선택할 때만 closure에 추가. 본 보고서에서 모드를 올리지 않음. |
| 선택 team-ui | `.claude/skills/team-ui/SKILL.md`, ui-programmer+ux-designer, 스킬이 사용하는 UX 자료 | individual의 필수는 2개. UMG role 설치와 specialists.ui 연결은 별도이며 이 팀 스킬은 individual에서 별도 engine UI spawn하지 않음. |
| 선택 team-qa/버그·증거 | `.claude/skills/team-qa/SKILL.md`, qa-tester/필요 gate의 qa-lead, `.claude/docs/templates/test-evidence.md`, `.claude/skills/bug-report/SKILL.md`, `.claude/docs/templates/bug-report.md`; 자동 실행 경로 채택 시 smoke-check/test-run 관련 파일 | bug-report 텍스트 라벨은 버그 집계 소비자와 맞춘다. Manual QA Phase 5는 테스트 담당자의 실제 답을 기다려 기록하며 무응답을 PASS로 채우지 않음(217–220행). 자동 테스트 scaffolding·CI는 minimal 역할 이관의 선행조건 아님. |
| 원본 preload를 보존하는 조건부 역할 | lead→`.claude/skills/tech-debt/SKILL.md`; qa-lead→`bug-report`, `release-checklist`; producer→`scope-check`, `estimate`; game-designer→`balance-check`; release-manager→`release-checklist`, `write-changelog`, `patch-notes`의 실제 SKILL.md | 각 역할 frontmatter의 `skills:`는 추가 입력 의존성이다. 사용하지 않는 preload를 역할 이관 때 제거하면 전체 스킬 closure를 가져올 필요가 없다. 유지한다면 그 skill의 references/scripts까지 별도 검토. |
| 원본 memory/isolation 보존 | `.claude/rules/agent-memory.md`; memory 역할의 `.claude/agent-memory/` 운영, prototyper의 worktree 설정 | 원본 개인 memory를 복사하지 않음. 임시 부재를 영구 사실로 저장하지 않고 재확인(해당 rule 28–65행). 최소 6개에는 memory frontmatter가 없음. prototyper는 조건부. |
| 기계적 역할 점검을 추가할 경우 | `.claude/scripts/agent-check.sh` | 선택 도구. 1–18행은 frontmatter/위임 관찰값을 출력한다고 설명한다. 게임 기능 성공을 판정하는 도구가 아니며 이번에 실행하지 않았다. |

Unreal 전용 경로로 축소할 때 Godot parse helper, Web one-shot/grow/page-shot 및 npm 빌드 권한을 역할 의존성으로 가져오지 않는다. 반대로 원본 dev-story를 다중 엔진 스킬 그대로 유지한다면 그 조건부 분기를 삭제하지 않은 채 helper를 빠뜨린 상태를 완전한 이관이라고 부르면 안 된다.

`.claude/settings.json` 전체 복사는 역할 이관의 요건이 아니다. 채택한 스킬 실행·필요한 에디터 도구·binary 편집 보호에 맞춰 권한만 검토·병합한다. 원본 역할의 “settings가 binary 편집을 막는다”는 말은 실제 대상 설정을 확인한 후에만 쓰도록 바꾼다. 라이선스 고지는 실제 파일 재사용 시 원본 LICENSE와 함께 보존한다.

### 6.3 specialists 연결 제안

- **지금 필요한 설정 변경은 없다.** engine.name으로 unreal primary가 추론된다. 실제 역할 파일과 호출자가 준비되기 전 이름만 추가해도 실행되지 않는다.
- `specialists.code`는 현재 unset 유지 또는 향후 명시할 필요가 있으면 unreal-specialist가 맞다. C++ 기반인데 ue-blueprint-specialist나 ue-replication-specialist를 code로 설정하면 모든 코드 스토리가 좁은 역할로 라우팅된다.
- `specialists.ui=ue-umg-specialist`는 UMG 역할을 실제 채택할 때만 병합하는 후보. dev-story UI 추가 검토·code-review UI lens에는 효과가 있지만 team-ui individual의 활성 집합을 넓히지는 않는다.
- shader는 현재 unset으로 두어 primary가 담당할 수 있다. 실제 material/VFX 제작은 조건부 technical-artist와 별도 task로 연결한다.
- `specialists.additional: [ue-replication-specialist]`는 이름을 표시하는 데는 유용하지만, `yaml-helper.sh` 2118–2128행은 목록을 **출력**할 뿐이다. dev-story 144–153행은 additional을 순회하지 않는다. 이 키 하나로 replication 검토를 보장한다고 보고하지 않는다. §3.3의 호출 조건과 read-only lens를 실제 소비자에 추가해야 한다.
- BP role은 자산/그래프·C++ hook 검토 업무일 때 직접 선택하거나 unreal 검토의 관련 lens로 연결한다. GAS 추가 등록은 지금 하지 않는다.

## 7. 이관 순서와 완료 확인 기준

실제 이관은 별도 승인된 작업에서 수행한다. 아래는 그때의 순서와 검토 기준이다. 이번 분석을 완료하기 위해 엔진을 실행하지는 않는다.

1. **입력과 버전 계약 고정:** 기존 project.yaml/CLAUDE/framework map/기획의 사실을 사용한다. 부재한 technical-preferences를 필수로 만들지 않는다. 원본 reference는 5.8.4 설치 근거와 충돌할 때 그대로 채택하지 않는다.
2. **6개 역할 초안과 호출자 조정:** §5의 변경을 역할 초안에 반영한다. 원본 dev-story 분기 4개와 primary 이름이 실제 파일로 해소되는지 정적 대조한다. replication의 선택 조건·file owner·검토 권한을 명시한다. GAS/BP/UMG 미설치 시에도 기본 검토가 막히지 않아야 한다.
3. **필요 스킬·script closure 연결:** dev-story와 code-review를 채택한 범위에 맞춰 §6의 실제 참조를 해소한다. story/기획/증거/상태 경로를 writer와 reader 양쪽에서 같게 바꾼다. 명령은 project.yaml의 기존 PowerShell commands를 우선하고 `<Project>Editor`를 폴더명에서 합성하지 않는다.
4. **조건부 역할을 업무별 추가:** BP/UMG가 필요한 업무부터 추가. QA 역할과 team-qa는 수동 결과 수집·버그/증거 저장까지 채택할 때, 감독·producer는 그 결정·운영 업무를 실제 선택할 때 추가한다. 모드 상승을 숨은 설치 조건으로 만들지 않는다.
5. **문서·권한 정적 검토:** 모든 hard reference가 실제 파일로 연결되거나 조건부/대체 입력으로 명확히 처리돼야 한다. helper-result 필드·gate verdict·bug label·run result 등 소비되는 형식을 검사한다. code-review는 1 helper, solo story-done은 0 director gate라는 계약을 확인한다.
6. **실제 기능 검증은 별도 단계:** 승인된 기능 구현 후 프로젝트 build 명령, 필요한 Automation, 실제 BP compile 및 2인 실행으로 검증한다. 실행 전에는 그 항목을 “예정/미검증”으로 유지한다.

| 완료 확인 구분 | 확인 기준 |
|---|---|
| 역할 이관의 정적 완료 | 채택 역할 수·이름이 목록과 일치, 다른 엔진/GAS가 기본 호출되지 않음, 모듈/타깃 TrackBuilding 유지, source/Content/Config scope 명시, version 문서의 부재가 숨지 않음. |
| 설정 보존 | Concept/hobby/minimal/collaborative 유지, 여섯 파생값을 명시 고정하지 않음, 기존 engine/commands 보존. specialists의 추가만으로 호출 완료를 주장하지 않음. |
| minimal QA 준수 | Logic/Integration 테스트 면제와 화면/2인 관찰 요구를 분리. 테스트 root 부재를 현재 사실로 기록하고 실행·통과로 오인하지 않음. |
| future build/BP 검증 | 실제 명령·exit·로그, BP 변경이면 editor compile 결과를 보관. nullrhi smoke의 종료 성공을 HUD·Steam·2인 성공으로 대신하지 않음. |
| future 2인 검증 | 양쪽에서 획득/배치/회전/회수, 동일 조각 동시 획득·동일 위치 경합의 단일 확정, 기차 구간/시간/승패, host UI의 state change, 연결 실패/호스트 종료 결과를 실제로 관찰. 필요 시 late join·지연·loss는 허용 정책을 정한 뒤 확인. |
| 보고의 정직성 | 미실행은 NOT VERIFIED, 입력/관찰 부재는 NOT ASSESSED, 자동 테스트 면제는 WAIVED로 구별. 전문 역할의 주장만으로 VERIFIED 결함/성공을 확정하지 않음. |

## 8. 미확정 사항과 이번 산출물 검증

다음은 실제 이관/구현 단계에서 정할 사항이다. 이번 보고서 저장을 막는 승인 요청은 아니다.

- Steam 구현을 OnlineSubsystem+Steam과 Online Services 중 무엇으로 할지, 실제 활성 플러그인·session API·travel 정책. 기존 framework map 68–69행도 선택을 남겨둔다.
- 늦은 접속 허용 여부, 클라이언트 중도 이탈 후 게임 흐름, host 종료 시 표시/메뉴 복귀 방식. “호스트 마이그레이션 구현”은 현재 요구가 아니다.
- 트랙 조각별 replicated Actor 대 배열 관리, 개수·relevancy·대역폭 근거, server-authoritative 랜덤 결과 전달. 기존 map 63–67행의 비교 항목을 따른다.
- CommonUI ini의 실제 의도·활성 상태, 화면 흐름·지원 언어/gamepad/접근성 목표, BP/UMG 편집 도구와 에셋 compile 경로. 역할 파일의 도구 목록만으로 실제 도구 연결을 가정하지 않는다.
- 새 작업/ADR/증거/checkpoint 문서의 저장 위치와 형식. 원본 design/docs/production tree를 통째로 만들기로 결정한 상태가 아니다.
- 테스트 생성 시점·module 포함 방식, 성능·지연·대역폭 목표. 원본 기본 숫자는 확정 요구가 아니다.

이번에는 `.claude/agents/` 실제 49개 파일의 역할 범위·버전 참조·frontmatter·협업 경계를 조사하고, dev-story/team/review 호출 지점과 대조했다. 분류 합계는 **지금 6 + 조건부 26 + 현재 제외 17 = 49**다. 엔진 소스 대조는 §5.5에 적은 항목에 한정한다. 지정한 이 보고서 하나만 생성했으며, 실제 역할 파일 복사·원본 변경·다른 보고서 갱신·엔진 실행·빌드·게임 테스트·커밋은 수행하지 않았다.
