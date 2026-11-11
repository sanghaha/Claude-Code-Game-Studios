# 04. 산출물 템플릿 선택 이관 검토

- 작성일: 2026-11-11 (Asia/Seoul)
- 원본 **S**: C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios
- 기준: CCGS v1.3.0, HEAD `abc0de79d6ceb0b5d7aa693945849b3937afbe6a`를 읽기로 확인했다.
- 대상 **T**: C:/Users/Sangha_Remote/Documents/TrackBuilding2
- 관할: S/.claude/docs/templates/의 **전체 74파일**(guidance 8, one-shot-lean 3 포함).
- 상태: **추천 보고서**. 실제 이관·스킬 변경·원본 수정/삭제·엔진 실행·빌드·게임 테스트·커밋을 수행하지 않았다. 이번 작업에서 저장하는 파일은 이 보고서 하나다.

## 1. 확인한 전제와 판정의 의미

S/T의 루트 및 상위 후보(C:/, C:/Users/, 사용자 홈, Documents)의 AGENTS.md 존재 여부를 확인하고, 양 저장소의 하위 경로도 `rg --files --hidden --no-ignore -g AGENTS.md`로 검색했다(생성·의존성 폴더 제외). 적용 가능한 AGENTS.md는 발견되지 않았다. 원본 SKILL.md·CONTRACT.md는 **분석 자료로 읽었으며 실행한 스킬이 아니다**. 거기에 적힌 작성 승인·에이전트 실행 절차를 이번 승인된 보고서 작업의 추가 승인 조건으로 적용하지 않았다.

대상 [CLAUDE.md](C:/Users/Sangha_Remote/Documents/TrackBuilding2/CLAUDE.md) 3–7, 11–20, 24–45행, [project.yaml](C:/Users/Sangha_Remote/Documents/TrackBuilding2/project.yaml) 6–43행, [기획.md](C:/Users/Sangha_Remote/Documents/TrackBuilding2/Doc/기획.md) §1–7, [unreal-framework-map.md](C:/Users/Sangha_Remote/Documents/TrackBuilding2/.claude/docs/unreal-framework-map.md) §배치 기준·확인한 엔진 사실을 현재 기준으로 읽었다.

| 항목 | 확정되어 있는 현재 전제 |
|---|---|
| 프로젝트 | 폴더는 TrackBuilding2, 실제 uproject·모듈·타깃은 **TrackBuilding** |
| 제품 | Unreal 5.8.4, C++ 기반+Blueprint, Steam PC, Listen Server 호스트 1+클라이언트 1 |
| 설정 | project.stage=Concept, project.goal=hobby, modes.rigor=minimal, modes.automation=collaborative |
| 파생값 | workflow=minimal, density=terse, QA=minimal, story_granularity=coarse, review=solo, team=individual |
| 기획 원본 | Doc/기획.md. 문서 수를 늘리기 위해 새 전체 콘셉트/GDD를 만들지 않는다. |
| 확인 한계 | TrackBuilding.uproject와 Source/TrackBuilding은 존재. Source/TrackBuilding/Tests는 **예정 경로이며 부재**. 구현 완료·빌드·Automation·2인 플레이 성공을 확인하지 않았다. |
| 누락 참고 문서 | **T/.claude/docs/technical-preferences.md는 조사 시점에 존재하지 않았다.** 원본 S의 동명 파일이나 다른 TrackBuilding 문서를 대상의 기존 문서로 간주하지 않는다. |

참고자료 S/ax-setup/ccgs-adoption-reference.md와 T/ax-setup/ccgs-settings-skill-impact.md·.json도 읽었다. 전자는 **TrackBuilding**에 관한 과거 제안·결정을 포함한다(§12, 465행 이후). 그 프로젝트의 REQ 체계, ADR-0001, Doc/Architecture/Decisions, Saved/Review, verify-full 등을 **TrackBuilding2의 확정 결정으로 승계하지 않았다**. JSON도 스스로 정적 문자열 참조 목록이라고 한정한다(method 항목). 아래 의존성은 실제 원본으로 다시 대조했다.

- **유지**: 지금 가져올 형식의 계약을 그대로 보존하는 추천.
- **수정**: 지금 필요한 형식이나 원본 그대로는 현재 경로·엔진·증거 요구와 맞지 않아 수정 후 이관하는 추천.
- **조건부**: 작성/소비 기능을 실제 채택할 때 가져온다. 수정 필요사항도 그때 함께 처리한다.
- **제외**: **이번 이관 목록에서 제외**한다. 원본 파일 삭제나 영구 사용 금지를 뜻하지 않는다.

## 2. 지금 추천하는 최소 묶음

74파일 중 **유지 1 / 수정 3 / 조건부 55 / 제외 15**다. 지금의 최소 형식 묶음은 다음 **4개**로 제한하는 것을 추천한다. 폴더 전체 복사는 권하지 않는다.

| 형식 | 이관할 원본 템플릿 | 역할 |
|---|---|---|
| brief 어댑터 | game-brief.md (수정) | Doc/기획.md에서 필요한 작업 범위·기능 목록·빌드 순서를 얻는 최소 입력 |
| Story | story.md (수정) | 수용 기준·의존성·실제 변경 파일·검증 결과를 한 작업에 연결 |
| 세션 상태 | session-state.md (유지) | 짧은 재개 지점과 상세 이력 분리 |
| 증거 기록 | test-evidence.md (수정) | UI/Visual 증거, 필요할 때 2인 수동 관찰을 실제 파일과 연결 |

**원본 minimal 경로는 engine→brief→create-stories→dev-story↔story-done이다.** /create-epics·스프린트·전체 GDD·중앙 TR registry·control-manifest·ADR·director gate는 최소 선행조건이 아니다(S/.claude/docs/workflow-catalog.yaml 20–76행; create-stories/CONTRACT.md 19–26행). Epic 서식 파일은 이 폴더에 없지만 **create-stories가 만드는 EPIC.md와 Stories 표는 숨은 필수 산출물**이다(§4.2).

Listen Server 권한/복제나 상태 소유권처럼 되돌리기 비싼 선택이 생기면 **짧은 ADR 1개를 조건부 추가**하는 편이 좋다. 지금 ADR 전체·TR 추적 체계를 선행 강제하거나 설정을 standard로 바꾸자는 뜻은 아니다. 버그가 생기면 bug-report.md+bug-closure-record.md, 검증 항목이 반복되면 test-plan.md를 추가한다.

UI/Visual이 아직 없는 첫 Logic 작업에는 evidence 문서를 미리 만들 필요가 없다. 반대로 **qa.level=minimal은 자동 테스트 면제이며 실제 실행 성공의 증거가 아니다**. UI/Visual의 retained screenshot은 면제되지 않는다. 별도 2인 관찰 기록 추천 역시 QA 등급 변경과 구별한다.

## 3. 전체 파일별 판정

아래 행 번호는 기준 커밋의 **실제 템플릿 파일** 기준이다. S/T는 §머리말의 절대 루트이며, 상대 파일명은 S/.claude/docs/templates/ 아래의 확인된 실제 경로다. 조건부 행의 “수정” 설명은 채택 시 처리할 내용이다.

| 파일 (S/.claude/docs/templates/ 아래) | 판정 | 현재 최소 경로 필요성 | 원본 근거 행 | 판단·조치 |
|---|---|---|---|---|
| `SKILL-CONTRACT-TEMPLATE.md` | **제외** | 없음 | 1–21, 324–489 | 스킬 작성용 설계 초안. 실제 7개 CONTRACT의 단순 형식과 다르며 TESTS/ASI 실행기는 없다고 자체 명시한다. 게임 산출물 묶음에 넣지 않는다. |
| `accessibility-requirements.md` | **조건부** | 초기 원칙만 Story에 | 3–31, 35–58, 175–227 | 접근성 약속을 별도 관리할 때 축약·수정. 등급은 QA minimal과 별개이며 자동으로 Basic/Certified를 확정하지 않는다. UX 서식과 등급 충돌을 해소한다(§6). |
| `adoption-plan.md` | **조건부** | 없음 | 13–51, 67–79 | 향후 /adopt 결과 형식용. minimal의 Step 3a–3d는 생략해야 하며 현재 분석 보고서와 중복 계획을 만들지 않는다. |
| `adoption-summary.md` | **조건부** | 없음 | 1–14 | /adopt 요약과 같은 형식. 파일 수·갭 등급은 형식 감사 관찰이며 빌드/게임 검증 결과로 읽지 않는다. |
| `architecture-decision-record.md` | **조건부** | 위험한 선택 시 | 1–27, 40–77, 145–176 | ADR을 자발적으로 채택할 때 Unreal 소유권·복제·엔진 근거를 추가한다. 현재 minimal은 ADR 없이 시작 가능; Accepted로 자동 승격하지 않는다. |
| `architecture-doc-from-code.md` | **조건부** | 없음 | 1–16, 125–166, 232–242 | 실제 구현에서 역문서화할 때만. 원본의 inline Status: Reverse-Documented는 정식 ADR 파서 계약과 다르므로 분석 기록으로 분리하거나 정식 Proposed ADR로 변환한다. |
| `architecture-traceability.md` | **조건부** | 없음 | 3–12, 25–35, 67–101 | 중앙 TR 추적을 채택할 때만 파생 색인으로 사용. 현재 brief 기준에 TR registry·Foundation gate를 추가하지 않는다. |
| `art-bible.md` | **조건부** | 없음 | 3–10, 12–67 | 시각 방향이 필요해질 때 9절·placeholder·AD-ART-BIBLE 계약 유지. 두 플레이어 식별, 배치 유효/거부, 주행/과열 상태 가독성부터 작성한다. |
| `bug-closure-record.md` | **조건부** | 버그 종료 시 | 1–8 | 검증 후 종료 기록은 재사용 가능. Verified by에 테스트명/수동 재현·빌드·역할을 적고 실제 재검증 없이 Closed로 쓰지 않는다. |
| `bug-report.md` | **조건부** | 버그 발생 시 | 3–22, 24–41 | BUG-NNNN·Severity/Priority/Status 필드 보존. Map, NetMode, host/client, 동시 요청 순서·양쪽 로그를 추가한다. |
| `certification-console.md` | **제외** | 없음 | 1–9 | 현재 Steam PC 범위에 콘솔 인증·사용자 전환·suspend 요구를 가져오지 않는다. |
| `certification-itch.md` | **제외** | 없음 | 1–6 | itch 배포 계획이 없으며 Butler/브라우저 배포 서식은 현 범위 밖이다. |
| `certification-none.md` | **조건부** | 내부 배포 시 | 1 | cert_tier=none이 선택된 내부 빌드에만 쓰는 생략 문구. Steam 게임이라는 이유로 내부 빌드까지 Steam 출시 서식을 강제하지 않는다. |
| `certification-steam.md` | **조건부** | Steam 배포 시 | 1–8 | Steamworks/설치 검증용으로 축약·수정. Achievements·Cloud·Deck은 채택한 기능만; Steam 로비/초대·2인 접속 증거를 별도 추가한다. |
| `certification-unknown.md` | **조건부** | 출시 범위 미정 시 | 1–3 | cert_tier 미확정 시 NOT ASSESSED 유지. 현재 project.yaml에는 cert_tier가 없으므로 steam을 자동 저장하지 않는다. |
| `changelog-internal.md` | **조건부** | 실제 변경 이력 후 | 1–43 | 내부 변경 기록이 필요하면 재사용. /write-changelog의 inline 형식도 함께 맞춰야 하며 Sprint는 있을 때만 쓴다. |
| `changelog-template.md` | **조건부** | 배포 시 | 1–29 | 플레이어 공지용. 구현 파일/내부 담당자 정보 대신 체감 변화를 적고 다른 patch 서식과 동시에 별도 원본을 유지하지 않는다. |
| `concept-doc-from-prototype.md` | **조건부** | 실제 프로토타입 후 | 1–16, 146–165, 221–239, 298–304 | 역문서화가 필요한 독립 프로토타입용. 코드 존재를 성공/재미 검증으로 오인하지 않고 NOT ASSESSED·관찰 근거를 추가한다. |
| `design-doc-from-implementation.md` | **조건부** | 실제 구현 후 | 1–16, 20–25, 99–124, 164–195 | 구현 관찰과 설계 의도를 분리할 때 사용. Reverse-Documented 상태를 승인으로 바꾸지 않고 Blueprint 미열람 부분을 명시한다. |
| `difficulty-curve.md` | **조건부** | 밸런스 반복 시 | 38–86, 88–147, 182–200, 285–330 | 장시간 RPG 예시 대신 3분 라운드·30초 준비/10초 주행/10초 과열·컨베이어 가중치·경로 난도 축으로 축약한다. 수치 원본은 Doc/기획.md이다. |
| `economy-model.md` | **제외** | 없음 | 16–61, 65–107 | 현재는 통화·상점·XP 경제가 없다. 컨베이어 생성/소멸은 해당 기획/Story의 조정값으로 관리하고 경제 시스템을 신설하지 않는다. |
| `faction-design.md` | **제외** | 없음 | 9–21, 62–119, 121–150 | 세력·평판·퀘스트 설정을 요구하는 서식이며 현재 협동 트랙 빌딩 범위에 근거가 없다. |
| `game-brief.md` | **수정** | 필수 추천 | 3–19, 30–43, 57–87 | Doc/기획.md를 기준으로 만든 최소 경로 어댑터. 6개 영문 필드·Features 체크박스·순서를 보존하고 제안/추론 표식을 유지한다(§4.1). |
| `game-concept.md` | **제외** | 없음 | 8–68, 139–203, 235–305 | minimal의 대체 대상이며 기존 기획과 별도 전체 콘셉트 원본을 만들게 된다. 장기 세션/엔진 재추천도 현재 전제와 맞지 않는다. |
| `game-design-document.md` | **조건부** | deepen 설계 필요 시 | 3–15, 25–77 | 기능이 brief를 넘을 때 해당 단위만 작성. 기존 Doc/기획.md를 복제하지 않으며 guidance와 소비자의 절/상태 계약을 함께 가져온다. |
| `game-pillars.md` | **제외** | 없음 | 3–7, 51–207, 283–313 | 현재 핵심 재미는 Doc/기획.md §1.2에 있다. 별도 승인·감정/장기 성장 문서를 최소 선행조건으로 만들지 않는다. |
| `guidance/accessibility-requirements-guide.md` | **조건부** | 접근성 서식 채택 시 | 1–9, 13–53, 168–210 | 접근성 본문과 한 묶음. Godot AccessKit·예시 등급을 Unreal 사실로 옮기지 않고 현재 엔진 근거로 교체한다. |
| `guidance/game-design-document-guide.md` | **조건부** | GDD 채택 시 | 1–9, 108–126, 218–264 | 절별 동적 로딩용. Dependencies/Tuning/UI/Cross-References 표 열과 checkbox AC를 보존한다. |
| `guidance/hud-design-guide.md` | **조건부** | HUD 서식 채택 시 | 1–6, 122–200, 272–380 | HUD 본문의 절 번호와 함께 수정. 호스트/클라이언트 표시 차이·타이머 동기화·배치 거부를 예시로 바꾼다. |
| `guidance/interaction-pattern-library-guide-game-specific.md` | **조건부** | 문맥 행동 표시 설계 시 | 3–5, 9–32, 123–145, 149–175 | Context Action Prompt만 우선 추출 가능. Inventory/Ability/Damage는 미채택; Godot 구현 예시는 UMG/입력 경로 검증 후 대체한다. |
| `guidance/interaction-pattern-library-guide-navigation-feedback.md` | **조건부** | 메뉴/로비 설계 시 | 3–5, 9–27, 79–135 | Focus/Escape/Loading/Error 패턴을 로비 접속·실패·호스트 종료 흐름에 맞춘다. 전역 일시정지로 오해하지 않게 한다. |
| `guidance/interaction-pattern-library-guide-standard-controls.md` | **조건부** | 공통 UI가 반복될 때 | 3–5, 39–41, 313–345, 507–511, 583–589 | 버튼·확인·슬라이더 등 필요한 패턴만 사용. Godot Control/CanvasLayer 예시를 Unreal 코드로 확정하지 않는다. |
| `guidance/interaction-pattern-library-guide.md` | **조건부** | 패턴 라이브러리 채택 시 | 1–20 | 세 개 실제 하위 guidance로 라우팅하는 색인. 채택한 패턴의 하위 파일을 빠뜨리거나 전부 선로딩하지 않는다. |
| `guidance/ux-spec-guide.md` | **조건부** | 화면/흐름 서식 채택 시 | 1–6, 206–287, 323–443 | 화면별 작성 지침. Data/Events와 입력·접근성·AC 절을 본문 및 ux-design references와 함께 맞춘다. |
| `help-escalation.md` | **조건부** | 도움말 이관 시 | 1–11 | 출력 예시이며 별도 런타임 필수 파일은 아니다. minimal 유지 결정을 자동으로 상향 설정하는 안내로 쓰지 않는다. |
| `help-where-you-are-minimal.md` | **조건부** | next-step UI 유지 시 | 1–16 | minimal 상태 안내의 재사용 형식. next-step 본문에 같은 문구가 inline으로 있어 이 파일만 수정해도 출력은 바뀌지 않는다. |
| `help-where-you-are.md` | **제외** | 없음 | 1–24 | Studio 단계/게이트 도움말. 현재 minimal 안내와 중복되고 Concept 고정을 미완료로 오인할 여지가 있다. |
| `hud-design.md` | **조건부** | HUD 구현 직전 | 3–21, 42–61, 296–351 | 타이머·구간·소지 조각·배치 경고 중심으로 축약. 전투/보스/콘솔/Deck 기본 체크를 현재 확정 범위로 해석하지 않는다. |
| `incident-response.md` | **조건부** | 외부 배포/실제 장애 시 | 3–8, 12–34, 53–135 | Listen Server 장애·Steam 장애의 영향/재현/복구 기록용. 전용 서버 운영팀·보상·대외 공지는 해당되는 경우에만 쓴다. |
| `interaction-pattern-library.md` | **조건부** | 반복 UI 규칙 필요 시 | 3–34, 66–115, 528–548, 669–753 | Pattern Catalog와 Used In을 관리할 때만. Context Action/Confirmation/Error/Loading부터 작성하고 로컬 preview와 서버 승인 상태를 분리한다. |
| `launch-checklist.md` | **조건부** | 출시 준비 시 | 7–37, 67–90, 113–130, 167–196 | 현재 Concept에 선행 강제하지 않는다. 리슨 서버에 전용 서버 운영·온콜팀을 기본 요구하지 않으며 출시 스킬 inline 본문과 함께 수정한다. |
| `level-design-document.md` | **조건부** | 맵/생성 규칙 구체화 시 | 3–9, 19–54, 70–83, 106–111 | 전투/서사/수집 대신 시작·도착·컨베이어·웨이포인트·장애물·도달 가능 경로·seed와 양쪽 일치 증거를 넣는다. |
| `milestone-definition.md` | **조건부** | 실제 마일스톤 운영 시 | 3–25, 27–79 | Build order로 충분한 현재는 미필수. sprint 수·조직 승인자를 억지로 채우지 않는다. |
| `narrative-character-sheet.md` | **제외** | 없음 | 3–9, 26–64, 82–104 | 내러티브 인물·대사 상태용이다. 플레이어 두 캐릭터의 외형/역할 설정을 이 서식으로 확대하지 않는다. |
| `one-shot-lean/config.js` | **제외** | 없음 | 1–11 | 브라우저 window.CONFIG/tune용 코드 자산. Unreal 수치는 DataAsset/DataTable/Blueprint defaults/Config 중 정한 한 원본으로 관리한다. |
| `one-shot-lean/index.html` | **제외** | 없음 | 1–27, 45–55, 150–186 | canvas·window.game·고정 step 웹 프로토타입 실행기이다. Unreal 산출물 템플릿으로 가져오지 않는다. |
| `one-shot-lean/tune.js` | **제외** | 없음 | 1–15, 19–48, 94–139 | 로컬 브라우저 저장·슬라이더 add-on이다. Unreal 튜닝/Replication 검증을 대신하지 않는다. |
| `patch-notes-brief.md` | **조건부** | 배포 변경 공지 시 | 1–15 | 현재 개발 시작에 불필요. /patch-notes Phase 4의 inline Brief 형식과 동기화한다. |
| `patch-notes-commentary.md` | **조건부** | 상세 공지 필요 시 | 1–4 | Full style에만 쓰는 개발 코멘터리. 실제 사용자 전달 필요가 있을 때만 추가한다. |
| `pitch-document.md` | **제외** | 없음 | 15–42, 103–144 | 사업 모델·투자/퍼블리셔 요청 서식이며 현재 hobby 개발 최소 경로와 무관하다. |
| `player-journey.md` | **조건부** | 온보딩 관찰 후 | 48–58, 58–225, 317–357 | 0분부터 50시간 이상까지의 일반형을 로비→첫 획득/배치→첫 실패/완주→재시작으로 줄인다. 사람이 플레이한 근거가 생긴 뒤 작성한다. |
| `post-mortem.md` | **조건부** | 시제품/마일스톤 종료 시 | 3–18, 47–69 | 짧은 회고에 사용 가능. producer 역할을 실재 팀원으로 가장하지 않고 1인 담당자·다음 조치만 둔다. |
| `project-stage-report.md` | **조건부** | 상태 진단 필요 시 | 3–20, 33–64 | Configured Concept와 Observed 상태를 분리. 코드 수·문서 유무로 실제 구현/빌드 성공률을 채우지 않는다. |
| `prototype-report.md` | **조건부** | 실제 프로토타입 실험 시 | 3–5, 9–54, 60–114 | 불확실성 질문·실제 관찰·PROCEED/PIVOT/KILL/NOT ASSESSED 형식은 유용. 2인 협동 질문에는 두 역할의 관찰을 남긴다. |
| `quick-design-change.md` | **조건부** | 기존 규칙 변경 시 | 3–6, 17–49 | GDD 대신 Doc/기획.md 절을 가리키고 변경 전/후·승인·영향·수용 기준을 짧게 작성한다. |
| `quick-design-new-system.md` | **조건부** | 작은 신규 기능 시 | 13–39 | assets/data/*.json 강제를 Unreal 조정값 소유 위치로 교체. systems-index 없는 minimal에 색인 생성을 강제하지 않는다. |
| `quick-design-tuning.md` | **조건부** | 튜닝 시작 시 | 3–24 | 기획 수치 근거·실제 값 저장 위치·호스트 판정값·관찰 결과를 기록. 클라이언트 로컬 값만 바꾸어 통과로 쓰지 않는다. |
| `release-checklist-template.md` | **조건부** | 배포 직전 | 11–35, 45–47, 76–92 | 기기 요구와 cert_tier를 분리하고 미실행은 NOT ASSESSED. QA minimal을 이유로 출시 검증이 이미 됐다고 쓰지 않는다. |
| `release-notes.md` | **조건부** | 배포 공지 시 | 1–38 | Detailed patch 형식. Combat 등 미존재 기능군 제거, 네트워크 변경·재접속/호스트 종료 사용자 영향을 포함한다. |
| `release-platform-requirements.md` | **조건부** | PC 배포 시 | 3–14, 16–33 | PC 블록만. 컨트롤러·해상도 범위는 실제 지원 범위로 좁히고 Console/Mobile 블록은 이관하지 않는다. |
| `risk-register-entry.md` | **조건부** | 위험 추적 반복 시 | 3–14, 20–58 | RISK-NNNN·확률/영향/트리거/대응 기록. 현재는 Story Open questions/ADR에 담고 별도 원본은 필요할 때만 만든다. |
| `session-state.md` | **유지** | 필수 추천 | 8–24, 29–44, 57–76 | STATUS/CHECKPOINT와 영문 키를 그대로 유지. 예시만 Unreal화 가능; 결과·다음 행동을 25줄 안팎으로 교체하고 이력은 아래에 둔다. |
| `soak-test-protocol.md` | **조건부** | 장시간 검증 시 | 28–52, 74–125 | 호스트/클라이언트 프로세스별 메모리·Actor 수·연결 상태를 추가. 짧거나 미실행인 soak는 NOT ASSESSED이다. |
| `sound-bible.md` | **조건부** | 오디오 제작 규칙 필요 시 | 3–4, 34–75, 103–133 | 원본 음원·Unreal 에셋 경로와 SoundWave/재생 구성을 구분하고 서버 이벤트당 로컬 중복 재생 여부를 명시한다. 전투/대사 표는 미필수. |
| `sprint-plan.md` | **제외** | 없음 | 6–18, 20–54 | 현재 minimal은 Build order를 사용한다. 스프린트 운영을 새로 도입하지 않는다. |
| `sprint-status.yaml` | **제외** | 없음 | 1–28 | Story 상태의 별도 미러를 만들지 않는다. 향후 스프린트 채택 시 file/id/status 매핑과 갱신자를 함께 이관한다. |
| `start-paths.md` | **조건부** | start 안내 이관 시 | 6–13, 32–48 | 이미 설정된 Unreal에 웹 기본 엔진/전체 setup 재실행을 제안하지 않게 수정한다. 현재 brief 어댑터 작성→Story를 제시한다. |
| `story.md` | **수정** | 필수 추천 | 1–28, 32–110 | 필드/상태/절 이름 유지, minimal의 N/A 매핑·실제 Source/Content/Config 경로·2인 AC·검증 결과 구분을 반영한다(§4.2). |
| `systems-index.md` | **조건부** | 의존 지도 필요 시 | 18–29, 31–57, 61–101 | 현재 brief Your systems와 중복 색인을 만들지 않는다. 채택 시 Category/Priority/Design Doc/Depends On 및 설계 순서 Layer 계약을 보존한다. |
| `technical-design-document.md` | **조건부** | 복잡한 단위 설계 시 | 3–23, 39–62, 77–96 | ADR과 다른 구현 설계가 필요할 때만. 현재 Status 필드가 없는데 Accepted 금지 규칙이 있어 상태 계약을 먼저 정해야 한다. |
| `test-evidence.md` | **수정** | UI/시각/2인 관찰 시 | 3–7, 16–42, 48–53, 70–96 | 이미지 실재·AC 표·Visual/Feel 서명 계약 유지. Integration/Replication 수동 기록을 확장하되 Type 값을 새로 만들지 않는다(§4.4). |
| `test-plan.md` | **조건부** | 검증 항목이 늘 때 | 13–29, 52–83, 102–122 | 현재는 Story AC와 짧은 2인 체크로 시작. 별도 계획 채택 시 automated 필수 열/DoD를 minimal 규칙대로 빼고 qa-plan inline 형식도 수정한다. |
| `ux-spec.md` | **조건부** | 메뉴/초대/결과 흐름 설계 시 | 3–21, 173–228, 280–311, 338–383 | UMG 로컬 표시와 소유 RPC 요청·서버 승인 데이터 분리. 접근성 등급의 상충 정의 및 모든 플랫폼 입력 강제를 정리한다. |
| `vertical-slice-report.md` | **조건부** | 실제 통합 slice 후 | 3–6, 52–99, 132–138 | 3분 라운드+접속/재시작 범위로 재정의. solo에서는 CD gate 미실행을 명시하고 두 사람이 플레이하지 않았으면 추천을 NOT ASSESSED로 남긴다. |

## 4. 최소 형식의 계약과 작성 전략

### 4.1 brief: 기존 기획의 작업용 어댑터

**추천 출력 위치는 T/Doc/game-brief.md이며 아직 만들지 않았다.** T/CLAUDE.md 26–32행의 기획·설계 문서 위치를 따른다. S의 기본값 design/game-brief.md를 그대로 만들면 별도 기획 트리가 생기므로, 이 경로 변경은 작성자와 소비자에 **동시에 반영할 후속 작업**이다(§5–6).

1. Doc/기획.md가 규칙·수치·승패·조작의 원본이다. brief에는 `Source document`, 원본 절 링크, 확인 날짜/가능하면 내용 해시를 추가하고 **파생 요약이며 충돌 시 원본 우선**이라고 적는다. brief만 바꾸어 게임 규칙을 바꾸지 않는다.
2. `Pitch`, `Core loop`, `Goal & fail state`, `Features`, `Out of scope`, `Build order` **6필드와 영문 표기**를 유지한다. 한국어 내용으로 작성한다(S/game-brief.md 4–19, 33, 57–87행). Features는 `- [ ]`/`- [x]`, 잘라낸 항목은 `- [-] … · cut: …` 형식을 보존한다.
3. Core loop는 획득→이동/회전→스냅 배치→기차 진행/구간 대응→완주/실패를 짧게 요약한다. 시간·충돌·서버 선착순 규칙의 상세는 Doc/기획.md §2–7로 연결한다. 원본 §2의 “3분”, §3의 준비 시간 포함 관계처럼 별도 해석이 필요한 부분은 질문으로 남기고 값을 새로 확정하지 않는다.
4. Features를 주관적으로 완료 처리하지 않는다. Unreal 프로젝트가 존재한다는 이유로 `[x]`를 쓰지 않는다. 기본은 미검증 `[ ]`이며 구현 관찰이 있다면 범위와 증거를 기록한다. 목록을 요약하더라도 기존 전체 기획에서 삭제/보류할 기능을 자동 결정하지 않는다.
5. `## In your words`는 원본에서 “사용자의 말을 원문 그대로 보존, 전체 긴 문서도 넣음”이다(30–31행; brainstorm/SKILL.md 62–64행). 여기서는 **기획 파일 전체 복사 대신 짧은 원문 발췌+출처, 나머지는 원본을 직접 읽도록 변경**을 추천한다. 현 계약 변경임을 명시하고 brainstorm·/cut·prototype 등을 나중에 채택할 때 같은 원본 읽기 규칙을 넣는다. 발췌를 요약문인데 원문이라고 표기하면 안 된다.
6. `*(guessed)*`, `*(inferred (strong))*`, `*(provisional)*`, `After play:` 등 출처·추론 표시를 유지한다. Steam/2인/Unreal 등 이미 주어진 전제는 추정으로 쓰지 않는다.
7. `Your systems`를 쓰면 `- <system> · in the slice / thin stand-in / later` 형식과 순서를 보존한다(69–70행). 이것이 thin→deepen Story와 추가 GDD 필요성을 **동적으로 켠다**. 현재 범위를 단순화하려고 임의로 later로 바꾸지 않는다. 엔진·goal은 project.yaml을 기준으로 읽으며 별도 상충 값을 brief에 저장하지 않는다.
8. 원본의 `Keep`는 config.js pin을 포함하는 웹 문구다(73–74행). Unreal에서는 확정된 DataAsset/DataTable/Blueprint 기본값/Config 경로와 프로퍼티 참조로 바꾸되, 값의 두 번째 원본을 만들지 않는다.

Feature의 **목록 위치 N**은 원본 `Brief MVP feature N` 참조의 ID 역할을 한다. Story 생성 후 Features를 앞에 삽입·재정렬하면 기존 Story의 의미가 바뀐다. 최소 도입에서는 순서·번호를 유지하고 새 항목을 뒤에 추가한다. F-001/REQ 같은 새 ID를 도입하려면 create-stories·story-done의 완료 체크·/cut 참조부터 함께 바꿔야 하므로 이번 필수 묶음에서는 새 중앙 ID 체계를 제안하지 않는다.

### 4.2 Story와 EPIC: 필드·ID·상태를 보존

기본 출력 위치 `production/epics/<epic-slug>/story-NNN-<slug>.md`를 당장은 유지하는 것을 추천한다. Story/QA/세션 운영 기록은 생산 추적 자료이며, 기획·설계는 Doc에 둔다. 이 위치도 변경한다면 아래 소비자 전체를 같은 단위로 바꿔야 한다. **NNN은 해당 epic 폴더에서 재사용하지 않는다.**

| 요소 | 현재 최소 형식에 남길 계약 | 읽는 근거 |
|---|---|---|
| 제목·헤더 | `# Story NNN: 제목`, Epic, Status, Layer, Type, Estimate, Manifest Version, Last Updated | story.md 1–10행; create-stories/CONTRACT.md 39–55행 |
| 상태 | Ready→In Progress→(필요 시 In Review)→Complete. Blocked는 사유 명시. Not Started/Done도 조회기가 읽지만 새 출력은 일관된 표기 사용 | story-status.sh 55–80행; sprint-status.yaml 4–10행 |
| Type | Logic / Integration / Visual/Feel / UI / Config/Data 5개만. Replication은 별도 Type으로 만들지 않고 Integration 등 해당 Type에 추가 문맥으로 기록 | story-facts.sh 163–166, 233–238행 |
| 문맥 | GDD는 추천 brief 경로, Requirement는 `Brief MVP feature N`(또는 Your systems 참조), 실제 기획 절 링크는 보조 필드로 추가 | create-stories/SKILL.md 297–310행 |
| minimal N/A | ADR Governing Implementation·ADR Decision Summary·ADR Version=`N/A (minimal — no ADRs)`; Manifest Version·Control Manifest Rules=`N/A (minimal — no control manifest)`. 헤더를 삭제하지 않는다 | 같은 파일 300–309행 |
| 엔진·위험 | Engine=Unreal 5.8.4. Risk는 해당 VERSION.md에 근거가 없으면 `NOT ASSESSED (no VERSION.md risk rating)`, LOW로 추정 금지 | create-stories/CONTRACT.md 55–56행. story.md의 LOW/MEDIUM/HIGH 예시 목록도 확장 필요 |
| 섹션 | Context, Acceptance Criteria, Implementation Notes, Out of Scope, Files, QA Test Cases, Test Evidence, Dependencies를 영문으로 유지 | story-facts.sh 278–300행; dev-story/CONTRACT.md 11, 41–42행 |
| 수용 기준 | `- [ ]` 항목. coarse 목표는 기능 하나에 5–10 AC이나 기획에 없는 기준을 숫자 맞추려고 만들지 않는다. AC-1 등은 행 순서와 연결하고 완료 중 임의 재정렬하지 않는다 | create-stories/SKILL.md 57–70, 109–145행 |
| QA Test Cases | `N/A — no qa-lead specs at this tier; implement against the Acceptance Criteria above`. qa-lead가 작성하지 않은 스펙을 작성한 것처럼 넣지 않는다 | create-stories/SKILL.md 313–315행 |
| Files | 계획 목록을 dev-story가 실제 변경 목록으로 교체. Source/TrackBuilding, 필요한 Config/*.ini, Content/*.uasset과 Unreal object path를 함께 적는다. Saved/Intermediate를 소스 원본으로 넣지 않는다 | story.md 59–66행; dev-story/CONTRACT.md 41행; T/CLAUDE.md 24–30행 |
| 증거 | Logic/Integration 자동 테스트는 waived를 명시. UI는 건드린 화면별 실제 이미지, Visual/Feel은 이미지+증거 문서 서명. 네트워크 AC에는 양쪽 관찰 기록을 추가 추천 | story.md 91–103행; story-done/SKILL.md 74–86행 |
| 의존성 | `Depends on: <실제 Story 경로 또는 None>`와 Unlocks. 완료 조건은 Complete/Done. 미래 Tests 경로는 계획으로 표시 | story.md 107–110행; dev-story/CONTRACT.md 27–31행 |

**EPIC.md도 함께 필요하다.** 별도 /create-epics 없이 create-stories가 최초 생성하며, 기존 파일은 범위를 다시 쓰지 않고 Stories 표만 갱신한다. 표 제목은 `## Stories`, 열은 `# / Story / Type / Status / ADR`다(create-stories/SKILL.md 326–346행). `production/epics/index.md`는 이미 있을 때만 갱신하며 최소 도입을 위해 새로 만들 필요가 없다. sprint-status.yaml도 만들지 않는다.

Story 종료는 **판정과 저장 상태를 구별**한다. 판정은 COMPLETE / COMPLETE WITH NOTES / NOT ASSESSED / BLOCKED이며 우선순위는 BLOCKED>NOT ASSESSED>COMPLETE WITH NOTES>COMPLETE다(story-done/CONTRACT.md 41–47행). 저장한 `Status: Complete`가 테스트 통과를 뜻하지 않는다. `## Completion Notes`에는 Completed, Criteria, Deviations, Test Evidence, Reviewed by와 실제 Run result/명령·결과를 남긴다. 독립 리뷰가 없으면 `self, no independent review`라고 기록한다. 원본 종료 계약은 테스트를 **실행하지 않고 존재만 검사**한다(같은 CONTRACT 37–39, 54–61, 83행).

Unreal 추가 필드는 기존 파서를 바꾸지 않는 **가산 방식**으로 추천한다: `Map / C++ class / Blueprint asset & parent / Execution role / Authority & state owner / Replicated fields & condition / RPC route / Data source / Host evidence / Client evidence`. 클래스 배치·권한은 T/.claude/docs/unreal-framework-map.md를 근거로 쓰고, 확인하지 않은 Blueprint 동작을 C++ 동작과 동일하다고 가정하지 않는다.

### 4.3 세션 상태: marker는 그대로, 진행 사실은 분리

기본 `production/session-state/active.md`, 이력 `production/session-logs/`를 유지하는 것이 최소 변경이다.

- 정확한 독립 행 marker: `<!-- STATUS -->`, `<!-- /STATUS -->`, `<!-- CHECKPOINT -->`, `<!-- /CHECKPOINT -->`.
- STATUS의 `Epic: / Feature: / Task:`는 선택 값이며 빈칸 가능하다. Concept에서 breadcrumb가 안 보인다고 단계를 올리지 않는다(session-state.md 57–62행).
- CHECKPOINT에는 `Updated, Branch, Current task, Next step, Blocked on, Files in progress, Open questions`를 남긴다. dev-story만 자신의 `Run result`를 기록하고 다른 작성자는 이를 결과처럼 재사용하지 않는다. Current task에 Story 경로를 명시하여 다른 Story의 증거를 오독하지 않게 한다.
- 현재 블록은 **교체**, 상세 Notes는 닫는 CHECKPOINT 아래에 덧붙인다. 주입 블록은 약 25줄, 이력은 약 200줄 이후 회전 권고(session-state.md 8–24행). marker를 설명한 문장과 실제 닫는 marker를 혼동하지 않는다(rotate-session-state.sh 47–63행).
- 원본 .gitignore 16–18행은 세션 로그/상태를 무시한다. 따라서 브랜치·머신 전환 때 남는 로컬 상태의 의미와 공유 정책을 후속 이관 때 정해야 한다. 이 보고서에서 .gitignore를 복사하거나 바꾸지 않았다.

### 4.4 QA 증거: 실제 실행·관찰·서명을 구별

test-evidence.md의 원래 대상은 **Visual/Feel·UI**다(3–7, 94–96행). 여기에 Integration 수동 검증용 모드를 추가할 수 있지만 원본이 이미 2인 검증기를 제공한다고 서술하면 안 된다.

| 영역 | 최소 기록 필드 |
|---|---|
| 식별 | Story의 실제 경로·Type, 날짜, 수행자, build/commit 및 dirty 변경 식별/파일 hash |
| 조건 | Unreal 5.8.4, Map/object path, PIE/Standalone/패키지 구분, 같은/다른 PC, 프로세스 수, Listen Server+client 역할, Steam 사용 여부, seed/초기 상태, 주입한 지연/손실이 있으면 값 |
| AC 결과 | 원래 `# / Criterion (from story) / Result / Notes` 표 유지. PASS/FAIL 외에 미실행·미관찰은 NOT ASSESSED와 이유를 추가 추천. waive는 PASS가 아니다 |
| 실행 결과 | Build / Automation / smoke / 2인 play를 별도 행으로: 실제 명령, 대상 이름, 종료코드, 결과 파일·로그, 성공/실패/NOT VERIFIED/NOT ASSESSED. 테스트 개수 0과 runner 종료 0을 게임 통과로 등치하지 않는다 |
| 네트워크 | 같은 AC에 대한 host/client 결과·로그·이미지 경로, 서버 판정 결과/요청 순서, 두 클라이언트 표시의 일치 또는 차이 |
| 이미지 | 원래 `# / Filename / What It Shows / Acceptance Criterion` 표 유지. 실제 디스크 파일을 보존하고 host/client를 구분. 시간 순서가 중요한 AC는 영상 타임스탬프나 로그 이벤트를 함께 기록 |
| 신선도 | 변경한 C++/헤더/Blueprint·Map·Config의 경로와 stamp/hash. Saved/Automation 결과는 필요 부분을 지속 보관 경로로 연결/복사하는 후속 정책 필요 |
| 서명 | Visual/Feel만 원래 `Role / Name / Date / Signature` 표의 **모든 추가 행**이 `[x] Approved`여야 한다. solo 본인 서명 가능. UI는 각 화면 이미지가 핵심이며 서명/증거 md가 필수는 아니다(test-evidence.md 70–89행) |

원본은 Deferred 서명을 허용한다고 적지만(test-evidence.md 88–90행), test-evidence-review/SKILL.md 198–206행은 **모든 행 [x] Approved**를 요구한다. 최소 도입에서는 “Deferred는 아직 승인되지 않은 증거”라고 명확히 하고 sprint가 없는 minimal에 “스프린트 리뷰 전에 해결”을 완료로 취급하지 않는다.

2인 핵심 AC 예시는 **기획에 이미 있는 행위**에서 가져온다: 같은 조각 동시 획득/같은 위치 동시 배치 시 서버 선착순 1건만 확정(기획 §6), 두 화면의 조각·회전·연결·기차 구간/타이머·승패 일치(§7), 호스트 종료 시 전체 게임 종료(§7). 접속 전/후, round 재시작, 실패·경고의 양쪽 표시를 관찰한다. 지연/손실/late join의 수치·지원 범위는 기획에 없으므로 **추가 범위 제안 또는 미확정**으로 둔다.

`commands.smoke`와 `commands.test`의 `-nullrhi` 실행은 화면 증거가 될 수 없다. `commands.run` 하나도 자동으로 두 프로세스·Steam 세션·호스트/클라이언트 행동을 만들어 주지 않는다(T/project.yaml 37–44행). 원본 run-and-observe.md 57, 174–207행의 Unreal HighResShot은 단일 Map 관찰 경로이며 **2인 하네스가 아니다**. Build/Automation 성공과 2인 AC 성공을 별도 기재한다.

### 4.5 ADR: 자발적으로 선택할 때의 최소 계약

`# ADR-NNNN: 제목`, `## Status` 아래 첫 텍스트의 Proposed/Accepted/Deprecated/Superseded by ADR-XXXX, `## Date`, `## Last Verified`, Summary, Engine Compatibility, ADR Dependencies, Context, Decision/Implementation Guidelines, Alternatives, Consequences, Validation Criteria, Related를 보존한다. 원본 템플릿은 GDD Requirements Addressed에 기획 추적 또는 `Foundational — no GDD requirement. Enables: …`를 요구한다(145–157행). minimal에서는 Doc/기획.md의 절을 출처로 넣을 수 있도록 명시적으로 조정하며 TR registry를 자동 신설하지 않는다.

Engine Compatibility 표의 **Layer**와 Domain은 다른 축이다. Layer는 Foundation/Core/Feature/Presentation 중 하나이며, 누락을 critical로 읽는 스킬이 있다(ADR 템플릿 55–68행). ADR Dependencies의 **Depends On** 행은 ADR-NNNN 또는 None으로 유지한다. 버전은 Last Verified→Date→unversioned 순이다. 추가할 Unreal 항목은 권한/객체 생존 범위, 복제 상태와 RPC 경로, C++/Blueprint 책임 경계, 설치된 5.8.4 엔진 근거, host/client 검증 계획이다. 엔진 근거 확인과 실제 동작 테스트는 별도 결과다.

기본 조회기는 docs/architecture/adr-*.md를 찾는다(adr-status.sh 86–93행). **Doc/Architecture/adr-*.md 같은 위치를 택한다면 새 제안 경로**로 취급하고 조회기·스킬·의존 그래프를 함께 바꾼다. 과거 TrackBuilding의 대문자 ADR 경로가 여기에도 있다고 가정하지 않는다. Accepted 처리 권한·확정 절차도 원본 템플릿 7–17행과 대상의 확정 정책을 후속 이관 시 대조한다.

## 5. 형식과 함께 가져올 의존성

아래는 템플릿이 실제로 작동하게 할 **후속 이관 검토 목록**이다. 여기서 설치하거나 역할을 실행하지 않았다. `S/` 경로는 전부 원본의 존재를 확인했다.

| 묶음 | 함께 필요한 실제 파일·역할 | 활성화/범위 |
|---|---|---|
| 설정·루트 해석 | .claude/hooks/yaml-helper.sh; .claude/engines/engines.tsv; .claude/scripts/engine-row.awk; .claude/docs/config-resolution.md, workflow-modes.md, automation-modes.md, code-root-resolution.md | create-stories/dev-story 등 원본 SKILL bootstrap이 실제 부른다. yaml-helper.sh 1426–1440행의 표·awk fallback도 포함. 대상 project.yaml은 입력으로 유지 |
| brief→Story | .claude/skills/brainstorm/SKILL.md; create-stories/SKILL.md+CONTRACT.md; .claude/docs/templates/game-brief.md, story.md; .claude/scripts/md-section.sh | minimal에서 brainstorm의 full-concept.md·감독 gate는 로드하지 않는다(21–28행). create-stories의 QA gate도 solo에서는 skip(240–246행) |
| Story 구현·종료 | .claude/skills/dev-story/SKILL.md+CONTRACT.md+references/engine-checks.md; story-done/SKILL.md+CONTRACT.md; .claude/scripts/story-facts.sh, story-status.sh, md-section.sh, adr-status.sh, session-state.sh; .claude/docs/run-and-observe.md, coding-standards.md; .claude/rules/test-standards.md | 원본 경로를 유지할 기능만 선택. ADR N/A라 해도 스킬이 helper를 호출하면 실행 파일 의존성은 남는다. 최소 설정의 테스트 미작성과 UI 이미지 계약을 전달 |
| Unreal 역할 | .claude/agents/engine-programmer.md, gameplay-programmer.md, network-programmer.md, ui-programmer.md, unreal-specialist.md; 필요 시 ai-programmer.md | dev-story/SKILL.md 135–153행이 Story layer/type과 specialists 결과로 동적 선택. individual/solo는 director gate skip이며 “어떤 코드 역할도 안 씀”은 아니다. 이관하지 않는 역할은 해당 라우팅을 명시적으로 대체해야 한다 |
| 세션 보존 | .claude/docs/templates/session-state.md; .claude/scripts/session-state.sh, rotate-session-state.sh; .claude/docs/context-management.md | 수동/스킬 갱신만으로도 사용 가능. active.md는 최초 writer가 템플릿으로 생성. 상태 보존과 자동 hook 등록을 구별 |
| 자동 세션 복원(조건부) | .claude/hooks/session-start.sh, session-stop.sh, pre-compact.sh; .claude/statusline.sh; yaml-helper.sh | 체크포인트 외 update/bug/sprint 문맥도 읽는다(session-start.sh 128–201, 281–377행). 필요한 부분만 선택/조정하고 settings.json은 채택한 등록만 병합 |
| 다음 행동/상태 탐지 | .claude/skills/next-step/SKILL.md; .claude/docs/workflow-catalog.yaml; .claude/scripts/artifact-check.sh, project-state.sh, story-status.sh; .claude/scripts/yaml-get.awk와 engine-row.awk/engines.tsv | project-state.sh 69, 163–165행의 실제 보조 파일. catalog의 minimal glob와 project-state.sh 262행 brief 인식을 Doc 경로와 동기화. Python 3 및 Git Bash/awk 등 실행 환경 확인 필요 |
| 증거 신선도 | .claude/scripts/review-receipts.sh; .claude/skills/test-evidence-review/SKILL.md(품질 검토를 채택할 때) | run-and-observe.md 45–47행은 stamp.txt 생성, evidence-review 218–230행은 hash/날짜 판정. 증거 이미지의 존재만으로 최신 코드 증거가 되지 않는다 |
| 별도 QA 계획(조건부) | .claude/skills/qa-plan/SKILL.md; test-plan.md; team-qa/SKILL.md, qa-lead.md/qa-tester.md(팀 경로 채택 시) | qa-plan은 41–46행 minimal smoke만. team-qa 242행이 test-evidence.md를 실제 읽는다. 팀 전체를 최소 형식 때문에 가져올 이유는 없다 |
| ADR(조건부) | architecture-decision/SKILL.md+references/5-generate.md 및 해당 스킬의 참조 파일; architecture-decision-record.md; adr-status.sh, adr-dep-graph.sh, md-section.sh | ADR을 만들거나 참조할 때만. layer/status/Depends On/날짜 계약과 템플릿의 동적 필수 절 판독을 함께 관리 |
| deepen/GDD(조건부) | design-system/SKILL.md+references/1-parse-arguments.md, 2-gather-context.md, 3-file-skeleton.md, 4-section-design.md, 5-post-design.md; game-design-document.md+guidance/game-design-document-guide.md; 필요 시 systems-index.md | create-stories의 Your systems→deepen이 설계를 요구하는 분기다. GDD 구조 검사/교차검토까지 채택하면 gdd-structure-check.sh·review-all-gdds 참조도 같은 경로/표 계약으로 수정 |
| UX(조건부) | ux-design/SKILL.md+references/2-gather-context.md, 3-file-skeleton.md, 4-authoring.md, 5-crossref-handoff.md, sections-hud.md, sections-patterns.md, sections-ux-spec.md; 선택한 본문+guidance | ux-design 29–35행은 절별 guidance를 직접 읽는다. 패턴 guidance index는 세 하위 파일로 다시 라우팅. 섹션별 지연 로딩이며 통째로 context에 넣을 필요 없음 |
| 출시(조건부) | release-checklist/SKILL.md, launch-checklist/SKILL.md; 선택한 certification·platform 서식; 필요 시 patch-notes/write-changelog | device(pc/console/mobile)와 cert_tier(none/itch/steam/console)는 다른 축. 미정이면 unknown/NOT ASSESSED. 대상 설정을 지금 채우지 않는다 |

**파일명 참조와 형식 복제는 구별해야 한다.** 모든 템플릿을 스킬이 파일에서 직접 읽는 구조가 아니다. 다음은 실제 소스에서 확인한 예다.

- 직접 참조: brainstorm→game-brief.md, create-stories→story.md(292–293행), session-state.sh→session-state.md(51–52행), adr-status.sh→architecture-decision-record.md(88행), team-qa→test-evidence.md(242행), ux-design→guidance 파일들.
- inline 중복: qa-plan/SKILL.md 176–293행, architecture-decision/references/5-generate.md 20–139행, next-step/SKILL.md 355–404행, release-checklist/SKILL.md 114–305행, launch-checklist/SKILL.md 120–344행, write-changelog/SKILL.md 140–218행, patch-notes/SKILL.md 146–216행. **템플릿만 고쳐서는 해당 스킬 출력이 바뀌지 않는다.**
- /patch-notes의 사용자 템플릿 탐지는 `docs/patch-notes-template.md` 또는 `.claude/docs/templates/patch-notes-template.md`다(112–121행). **원본 templates에는 그 이름의 파일이 없다.** release-notes/patch-notes-brief를 자동 발견한다고 가정하지 않는다.
- technical-design-document/economy-model/faction-design/narrative-character-sheet/pitch-document 등의 파일명을 대상으로 skills/agents/scripts/hooks에서 직접 참조를 검색했으나 명시적인 파일 로더는 찾지 못했다. 수동 저작 자원 또는 역할의 일반적인 형식 참고로 평가했으며, 이름이 비슷한 역할이 자동으로 해당 파일을 읽는다고 단정하지 않았다.
- one-shot-new.sh 63–76, 150행은 one-shot-lean의 파일과 brief를 실제 복사한다. 이를 제외하면 브라우저 one-shot을 함께 채택할 수 없다는 의존성은 분명하지만 **현재 Unreal 경로에는 그 기능이 필요 없다**.

## 6. Unreal용 변경안과 소비자 영향

이 표는 후속 이관의 수정 명세다. 실제 스킬·스크립트 변경을 이 보고서 작성에 포함하지 않았다.

| 원본 위치·근거 | 현재 문제 | 변경안 | 함께 영향을 받는 소비자 |
|---|---|---|---|
| game-brief.md 30–31, 57–87; brainstorm 44–46, 62–64; workflow-catalog.yaml 46–52; project-state.sh 262 | design/game-brief를 새 기획 원본처럼 쓰거나 긴 기획 전체를 복제할 위험 | Doc/game-brief 어댑터, 원본 절/해시/수정 우선순위. 원문은 발췌+링크와 직접 읽기로 변경 | brainstorm, create-stories와 CONTRACT, story-done의 Features 완료(345–349행), next-step, catalog, project-state. 채택 시 start/adopt/project-stage-detect/detect-gaps와 /cut까지 같은 매핑 |
| story.md 14–28, 97–101; create-stories 300–324; test-plan.md 15–16, 32 | GDD/TR/ADR/manifest와 tests/unit 경로를 모든 작업에 요구하는 모양 | minimal N/A 정확 매핑, Source/TrackBuilding/Tests는 예정 경로, 현재 테스트는 waived. 새 테스트를 선택할 때만 Automation F[System][Scenario]Test·TrackBuilding. 접두와 실제 경로 기록 | create-stories/dev-story/story-done 계약·inline 본문, story-readiness, test-evidence-review, qa-plan, test-standards |
| story.md 22; create-stories CONTRACT 55–56 | Risk 예시가 LOW/MEDIUM/HIGH만 보여 미평가 상태가 누락 | NOT ASSESSED를 정식 허용값으로 추가하고 별도 엔진 근거·검증 계획 남김 | story 생성·준비·구현·엔진 전문 검토. 5.8.4이므로 임의 LOW/HIGH 사실 판정 금지 |
| story.md 59–66; ADR Engine Compatibility 40–77; technical-design-document.md 46–62 | C++ 코드 외 Blueprint/Map/복제 상태의 소유·검증 범위를 놓침 | Content 실제 파일+Unreal object path+parent class, C++ 선언/Blueprint 구성 경계, Authority/Owner/RPC/Replicated state와 참조 framework-map 추가 | Files 범위 검사·review hash·실제 에셋 열람·unreal/network/ui 역할 인계 |
| test-evidence.md 3–7, 22–42, 48–53 | 단일 화면·Visual/UI만 전제. host/client 인과와 Steam 접속이 없음 | §4.4의 역할별 결과·환경·Map·seed·로그/영상·두 화면 증거. 불관찰은 NOT ASSESSED | story-done, evidence-review, run-and-observe, 필요 시 qa-plan/team-qa |
| test-evidence.md 88–90 vs evidence-review 198–206 | Deferred 서명과 “모든 행 Approved”가 충돌, minimal에는 sprint review 없음 | Deferred는 미승인으로 명시. Visual/Feel은 실제 본인/담당자 승인 후 닫음. UI에 추가 서명 의무를 만들지 않음 | Visual gate·story 종료·QA 보고서, 불필요한 빈 서명 행 생성 방지 |
| run-and-observe.md 57, 174–207; T/project.yaml 41–44 | 단일 실행/HighResShot 및 nullrhi smoke를 2인 화면 검증으로 오독할 수 있음 | 실행/빌드/Automation/양쪽 관찰 결과를 별도 필드로 분리. 고정 해상도·이미지 경로 보존은 유지 | dev-story 요약·checkpoint의 Run result·story-done·evidence-review. 실제 2인 하네스는 후속 설계 |
| architecture-doc-from-code.md 1–8 vs adr-status.sh 251–270 | Reverse-Documented inline Status와 정식 ADR의 ## Status/엔진/의존 표가 다름 | 역문서화 보고서와 채택 ADR 분리. 정식 결정은 Proposed부터, 검증 사실과 의도를 구분 | reverse-document, architecture-decision retrofit, adr-status/dep graph, Story ADR 판정 |
| architecture-decision-record.md 145–157; architecture-traceability.md 25–35 | GDD/TR 추적을 적용하면 brief minimal에 없는 중앙 registry가 생김 | Doc/기획 절 링크 또는 foundational 예외. 중앙 표는 채택 시 원본에서 파생 | architecture-review, propagate-design-change, create-control-manifest, Epic/Story는 기능 채택 시 조정 |
| technical-design-document.md 3–23 | 문서에 Status 필드가 없으나 Accepted를 금지하는 규칙은 있음 | 조건부 이관 전에 Status/검증 상태·확정 주체를 정의하거나 ADR의 채택 상태만 참조. 설계 검증이 기획 승인/구현 완료와 섞이지 않게 함 | 별도 TDD 저작/리뷰 절차. 실제 파일 로더는 미발견 |
| quick-design-new-system.md 19–25; quick-design-tuning.md 20–24; game-brief.md 73–74 | assets/data JSON/config.js를 유일한 튜닝 원본으로 강제 | Unreal DataAsset/DataTable/Blueprint defaults/Config 중 **해당 값의 한 원본**을 지정. 값 적용 머신/재실행 필요 여부 명시 | quick-design, 새 기능/튜닝 Story, QA 실제 값 관찰 |
| ux-spec.md 288–292 vs accessibility-requirements.md 41–46 | “Standard에 screen reader” vs “Comprehensive에 screen reader” 등 등급 정의 충돌 | 프로젝트 접근성 문서의 선택 등급/개별 약속을 유일 기준으로 쓰고 UX inline 재정의를 제거. qa.level과 분리 | UX/HUD·접근성 템플릿+guidance, ux-design refs의 skeleton·AC·검토. 현재 등급 미확정 |
| guidance/interaction-pattern-library-guide-standard-controls.md 39–41, 325–328, 507–511; game-specific 32, 144; navigation-feedback 26; accessibility guide 180–181, 208 | Godot Control/Node/AccessKit 구현 예시가 Unreal과 맞지 않음 | UMG/입력/접근성 API를 설치된 5.8.4 근거로 확인 후 작성. 미확인은 Open Questions. UI 로컬 표시·서버 권한은 framework-map 준수 | ux-design·ui-programmer·unreal-specialist, 패턴 구현/접근성 검토 |
| hud-design.md 317–351; ux-spec.md 345–383 | 전투/보스·콘솔/Deck·게임패드·모든 해상도·상위 접근성 예시가 기본 AC처럼 보임 | PC Keyboard/Mouse·쿼터뷰·타이머/과열/소지 조각·경고로 좁힘. 지원하지 않는 항목은 이유 있는 N/A, 숫자 예시는 확정하지 않음 | HUD/UX guidance·ux-design sections-hud/sections-ux-spec·스토리/증거 |
| certification-steam.md 5–7; release-platform-requirements.md 11–12; release-checklist-template.md 45–47 | Steam이면 도전과제/Cloud/Deck·게임패드까지 자동 요구할 위험 | 실제 선택 기능에만 체크, Steam 로비/초대·호스트 종료·양쪽 동기화는 별도 검증. cert_tier는 미정 유지 | release-checklist/launch-checklist **inline 블록도 함께**. 현재 출시 필수 흐름으로 추가하지 않음 |
| systems-index.md 82–84 vs ADR template 65–68 | Dependency Map에는 Polish Layer가 있지만 ADR/Story Layer는 4값 | Polish는 지도상의 후반 분류로만 두고 ADR/Story에 다섯 번째 Layer를 출력하지 않게 주석 명확화 | map-systems·설계 순서 표·create-stories·ADR critical 판정 |

**경로/기술 문서/설정은 별개 결정이다.** 템플릿 채택은 T/CLAUDE.md, project.yaml, unreal-framework-map.md를 원본으로 덮어쓰는 근거가 되지 않는다. technical-preferences.md도 현재 없으므로 기존 문서 덮어쓰기 문제가 아니라 **필요한 경우 대상용 새 문서를 만들지**의 문제다. dev-story/CONTRACT.md 15–17행상 forbidden patterns/allowed libraries는 그 파일의 별도 소비 대상이므로, 관련 스킬을 채택할 때 framework-map·현재 project.yaml을 참조하는 짧은 문서/라우팅을 준비해야 한다. 원본의 엔진 미설정 placeholder를 복사해 설정된 Unreal을 미설정으로 되돌리지 않는다.

settings.json·project.yaml·.gitignore·모든 기술 문서의 통복사는 추천하지 않는다. 프로젝트 공통 지침은 이미 완료된 별도 단위로 유지한다. 변경할 경로·필드의 **소비자 수정 명세만** 해당 이관 단위에 전달 가능한 수준으로 남긴다.

## 7. 이관 순서와 완료 확인 기준

이번 보고서 이후 실제 이관을 진행할 때의 추천 순서다.

1. **원본/출력 위치 확정**: Doc/기획.md 우선, brief=Doc/game-brief.md(추천), 운영 기록=production/epics·qa·session-state(추천)를 선택한다. 현재 파일을 새 규칙으로 일괄 재작성하지 않는다.
2. **형식 4개 준비**: game-brief·Story·session-state·test-evidence의 수정안을 작성한다. §4의 영어 키/marker·상태·테이블을 보존하고 Unreal 필드는 추가 방식으로 둔다.
3. **활성 소비자를 같은 묶음으로 조정**: brief 경로·최소 N/A·QA waive·위험 미평가·Files/Blueprint·양쪽 증거를 create-stories/dev-story/story-done/next-step/catalog/helpers와 계약 문서에 함께 맞춘다. 사용하지 않을 분기는 로드되지 않게 명시한다. hook 자동 등록은 이 단계의 필수 요소가 아니다.
4. **정적 계약 확인**: 기획 원본과 brief 요약의 충돌·출처 누락, 영문 헤더, Feature N/Story NNN 중복, 최소 N/A, Type 5값, 실제/예정 파일 구분, 의존 경로·상태, EPIC Stories 표, session marker 순서를 검사한다. 원본 lint의 no_tr_id는 minimal의 정상 비TR 참조에 그대로 차단 적용하지 않는다(story-facts.sh 241, 296행). 선택한 workflow 소비자가 이를 해석해야 한다.
5. **후속 이관 환경에서 작은 샘플로 소비자 확인**: 실제 게임 작업을 시작하기 전 문서/파서 범위에서 한 Story와 상태 블록을 읽고, next-step이 GDD·스프린트·전체 단계 gate를 강제하지 않는지, 회전이 Notes만 옮기는지 확인한다. 이 보고서 작성에서는 스크립트를 실행하지 않았다.
6. **실제 개발/검증은 별도 작업**: 처음 수행하는 구현의 build·Automation·2인 관찰 결과를 각각 남긴다. 검증하지 못하면 NOT VERIFIED/NOT ASSESSED로 끝내며 문서·이미지 존재를 성공으로 환산하지 않는다.

완료 판정은 두 층으로 분리한다.

| 층 | 완료 확인 기준 |
|---|---|
| 템플릿/소비자 이관 완료 | 채택 파일 목록만 이관, 6필드 brief와 원본 추적, Story/EPIC/상태 조회의 일치, checkpoint 교체·회전, UI/Visual 증거 경로/서명 판독, Unreal 추가 필드 소비, 활성 경로에 낡은 design/tests/Godot 전제 없음, 설정값 불변 |
| 게임 구현/검증 완료 | 해당 Story의 실제 AC를 build/Automation/수동 또는 2인 관찰로 평가한 독립 결과. 현재는 **미확인**이며 이 보고서의 완료와 무관 |

## 8. 미확정 사항과 이번 검토의 한계

- brief 및 ADR·QA의 **최종 출력 위치**는 추천 단계다. 기존 TrackBuilding의 Doc/Architecture/Decisions·Doc/QA를 확정 경로로 재사용하지 않았다.
- Steam 세션 구현/플러그인 선택, 패키지 실행/PIE·다른 PC 검증 방식, 늦은 접속·재접속 지원, 지연/손실 목표는 이 템플릿 분석으로 확정할 수 없다.
- 접근성 목표 등급, 지원 해상도/게임패드/Deck, 아트·음향 예산과 필수 서명 주체, 로컬 상태/대용량 증거의 Git 보관 방식은 미확정이다.
- 현재 Source/TrackBuilding/Tests가 없고, 설정 명령만 존재한다. “Automation 검증을 수행했다”, “2인 성공”, “게임 기능이 구현되었다”라고 판단하지 않았다.
- 원본 파일명 참조 검색은 정적 분석이다. 런타임의 모델 판단·사용자 override·선택 역할의 전체 동적 실행을 검증한 의존성 폐쇄로 주장하지 않는다. §5에 직접 참조·inline 복제·조건부 의존성을 구별했다.
- 이번 보고서의 저장 전 확인은 **74파일 판정 누락/중복·보고서 내부 경로/근거·변경 범위**에 한정한다. 엔진이나 게임 테스트를 하지 않았다.

## 9. 핵심 근거 바로가기

- [원본 템플릿 폴더](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/docs/templates)
- [brief 계약](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/docs/templates/game-brief.md:4)
- [Story 형식](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/docs/templates/story.md:1)
- [Story 생성의 minimal 분기](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/skills/create-stories/SKILL.md:91)
- [생성 필드 매핑·EPIC 표](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/skills/create-stories/SKILL.md:297)
- [구현 계약](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/skills/dev-story/CONTRACT.md:20)
- [종료 계약의 존재 검사 한계](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/skills/story-done/CONTRACT.md:39)
- [세션 marker](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/docs/templates/session-state.md:29)
- [증거·서명](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/docs/templates/test-evidence.md:30)
- [원본 minimal 경로 catalog](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/docs/workflow-catalog.yaml:20)
- [접근성 기준표](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/docs/templates/accessibility-requirements.md:39)
- [상충하는 UX 접근성 정의](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/docs/templates/ux-spec.md:288)

