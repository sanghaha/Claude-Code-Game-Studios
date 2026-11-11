# CCGS 파일별 규칙 선택 이관 검토

작성일: 2026-11-11 (Asia/Seoul). 범위: 원본 `.claude/rules/`의 **14개 파일 전부**에 대한 정적 분석과 이관 제안. 이 문서의 추천은 채택 결정이나 설치 완료를 뜻하지 않는다. **제외는 이관 목록에서 뺀다는 뜻이며 원본 삭제가 아니다.**

## 1. 기준과 확인 범위

- 원본 S: [Claude-Code-Game-Studios](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios), v1.3.0, 실제 HEAD `abc0de79d6ceb0b5d7aa693945849b3937afbe6a`. 아래 `S/…`는 이 절대 경로 아래의 실제 파일을 뜻한다.
- 대상 T: [TrackBuilding2](C:/Users/Sangha_Remote/Documents/TrackBuilding2). 아래 `T/…`는 이 절대 경로 아래를 뜻한다. **제안 경로·가상 예시는 별도로 표시**한다.
- 두 저장소 및 작업 경로 상위의 적용 가능한 `AGENTS.md`를 확인했다. 상위 경로의 후보 파일은 없었고, 두 저장소의 숨김·무시 파일을 포함한 `rg --files --hidden --no-ignore -g AGENTS.md` 검색에서도 발견하지 못했다. 원본 `CLAUDE.md`와 그 공통 지침, 대상의 현재 `CLAUDE.md`를 읽었다. 이번 보고서 저장은 이미 승인된 범위이므로 원본의 쓰기 질문 절차를 다시 실행하지 않았다.
- 대상 공통 지침은 완료된 전제로 취급하며 재작성하지 않는다. 기준은 [CLAUDE.md 3–45행](C:/Users/Sangha_Remote/Documents/TrackBuilding2/CLAUDE.md:3), [project.yaml 6–44행](C:/Users/Sangha_Remote/Documents/TrackBuilding2/project.yaml:6), [기획 138–155행](C:/Users/Sangha_Remote/Documents/TrackBuilding2/Doc/기획.md:138), [Unreal Framework Map](C:/Users/Sangha_Remote/Documents/TrackBuilding2/.claude/docs/unreal-framework-map.md:8)이다.
- 유지할 설정: `Concept / hobby / rigor=minimal / automation=collaborative`; 파생값은 `workflow=minimal / density=terse / QA=minimal / story_granularity=coarse / review=solo / team=individual`. 대상 YAML에는 파생값 여섯 개를 추가하지 않는다. 원본 해석 순서와 기본값은 [config-resolution.md 103–178행](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/docs/config-resolution.md:103), 기존 대상 JSON의 `saved_settings`, `derived_settings`와 대조했다. 이번에는 해석기나 스킬을 실행하지 않았다.
- 현재 실제 파일은 `TrackBuilding.uproject`, `Source/TrackBuilding/{TrackBuilding.h,TrackBuilding.cpp,TrackBuilding.Build.cs}`, `Source/TrackBuilding.Target.cs`, `Source/TrackBuildingEditor.Target.cs`, `Config/Default*.ini`, `Doc/기획.md`다. 폴더명 `TrackBuilding2`에서 프로젝트·모듈명을 유추하지 않는다. `Source/TrackBuilding/Tests`는 YAML에 있는 **예정 경로이며 현재 폴더가 없다**.
- 대상 `.claude`의 현재 실제 파일은 `.claude/docs/unreal-framework-map.md`뿐이다. `T/.claude/docs/technical-preferences.md`는 읽기 시도와 파일 목록 확인 결과 **없다**. 기존 [설정 영향 보고서 24행](C:/Users/Sangha_Remote/Documents/TrackBuilding2/ax-setup/ccgs-settings-skill-impact.md:24)의 존재 서술을 현재 사실로 재사용하지 않는다. 누락 파일을 이번 작업에서 생성하지 않았다.
- [기존 원본 참고 보고서 §4 및 285–287행](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/ax-setup/ccgs-adoption-reference.md:285)와 대상 [설정 영향 보고서](C:/Users/Sangha_Remote/Documents/TrackBuilding2/ax-setup/ccgs-settings-skill-impact.md), [동반 JSON](C:/Users/Sangha_Remote/Documents/TrackBuilding2/ax-setup/ccgs-settings-skill-impact.json)을 참고하되, 아래 판정은 실제 원본 규칙·소비자 파일을 읽어 다시 내렸다. 참고 보고서의 **기존 TrackBuilding** 규칙·스킬·ADR 채택은 **TrackBuilding2의 채택 사실이 아니다**.

실제 이관, 규칙·에이전트·스킬·훅 수정, 원본 수정·삭제, 엔진 실행, 빌드, 게임 테스트, 커밋은 하지 않았다. 아래 엔진 동작 근거는 대상 Framework Map의 기록을 인용한 것이며 이번 세션에서 엔진 소스를 새로 검증했다는 뜻이 아니다.

## 2. 지금의 추천과 최소 묶음

| 판정 | 원본 파일 | 현재 판단 |
|---|---|---|
| **지금 추천: 5개** | `design-docs.md`, `gameplay-code.md`, `network-code.md`, `test-standards.md`, `scene-files.md` | 경로와 내용을 Unreal·현재 모드에 맞게 고친 뒤 가져온다. 원문 통복사 추천은 아니다. |
| **조건부: 6개** | `engine-code.md`, `ui-code.md`, `data-files.md`, `shader-code.md`, `skill-authoring.md`, `agent-memory.md` | 해당 업무·운영 기능을 실제 채택할 때 추가한다. 구현 시작 전의 상시 필수 규칙으로 늘리지 않는다. |
| **현재 제외: 3개** | `ai-code.md`, `narrative.md`, `prototype-code.md` | 현재 최소 경로에 해당 산출물·기능이 없다. 트리거가 생기면 재검토한다. |

**최소 이관 묶음 제안**은 위 5개 규칙의 대상용 개정본이다. `scene-files`는 원본의 Unreal 규칙이 아니라 **원본의 빈 Unreal 적용 범위를 메우는 재작성**이다. 공통 의존 입력은 대상에 이미 있는 `project.yaml`, `Doc/기획.md`, `.claude/docs/unreal-framework-map.md`를 사용한다. 규칙 자체는 실행 파일이 아니므로 **이 묶음만을 위해 필요한 스크립트·역할의 수는 0개**다.

다만 향후 `brief → create-stories → dev-story → story-done` 경로를 설치할 때는 §5의 설정 해석·규칙 명시 읽기·문서 경로 계약을 함께 연결해야 한다. 규칙 파일 5개만 복사한 상태를 자동 적용·자동 검증 완료로 부르면 안 된다. `test-standards` 추천도 지금 테스트 폴더·테스트·CI를 만들라는 뜻이 아니라, **테스트를 쓰거나 결과를 해석할 때의 UE 기준과 미실행 표기를 먼저 맞추자는 뜻**이다.

`skill-authoring`의 다섯 의무는 스킬 이관 작업이 시작되는 시점에는 함께 적용할 가치가 높다. 현재 최소 게임 제작 경로의 여섯 번째 필수 게임 규칙으로 묶지는 않는다.

## 3. 원본 파일별 판정

행 번호는 위 기준 커밋의 원본 파일 기준이다. `paths`는 원본 값을 요약했으며 §4에 대상용 제안을 구분했다.

| 원본 실제 파일·근거 | 원본 적용 범위와 핵심 내용 | 판정·현재 최소 경로에서의 필요성 | 유지·변경·제외할 부분 |
|---|---|---|---|
| [design-docs.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/rules/design-docs.md:1), 3, 8–21행 | `design/gdd/**`; workflow별 GDD 항목 수, 수식·예외·수용 기준, 절별 승인 | **지금 추천.** 기존 `Doc/기획.md`와 최소 brief/스토리의 기준 연결에 필요. 현재 원본 glob은 대상 기획을 놓침. | `Doc/**/*.md`로 변경하되 문서 유형을 먼저 구분. minimal은 GDD 0개 필수, 기존 기획 보존. 8개 절·양방향 GDD·안전 범위는 해당 상세 설계를 작성할 때 적용. 현재 기획을 8절 문서로 강제 변환하거나 절별 재승인하지 않음. |
| [gameplay-code.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/rules/gameplay-code.md:1), 3–6, 11–19, 37–42행 | `src/gameplay`, `src/game`, Unity Gameplay C#, UE `Source/**/Gameplay/**/*.{h,cpp}`; 데이터 구동, delta time, UI 분리, 모든 로직 테스트 | **지금 추천.** 트랙 획득·배치·기차·컨베이어가 핵심. 기능별 폴더·모듈 루트도 포함해야 함. | DataAsset·DataTable·편집 가능한 기본값으로 조정 수치를 관리. 모든 리터럴의 외부화·모든 시스템에 별도 인터페이스·모든 로직 단위 테스트 강제는 제외/완화. `qa.level`에 맞추고 네트워크 권한은 별도 규칙과 함께 적용. Godot/C# 예제 제거. |
| [network-code.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/rules/network-code.md:1), 3–5, 10–17행 | `src/networking`, Unity Network(ing) C#, UE Network(ing) h/cpp; 서버 권한, 메시지 버전, 예측·rollback, 재접속·호스트 이전, 로그·대역폭·입력 검증 | **지금 추천, 우선순위 최상.** 트랙·컨베이어·기차 등 기능 단위 안의 복제를 놓치면 게임 핵심 규칙을 놓침. | 모듈의 모든 h/cpp와 관련 Config/uproject까지 확대. 서버 판정·소유 RPC 경로·호스트 표시 갱신·상태/이벤트 구분 보강. 호스트 이전 제거, 재접속/늦은 입장은 미확정으로 분리. 예측·rollback과 커스텀 메시지 버전은 조건부. |
| [test-standards.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/rules/test-standards.md:1), 3–5, 16–24, 29–46, 90–113행 | `tests/**`, Unity Tests C#, `Source/**/Tests/**/*.{h,cpp}`; UE Automation 이름, AAA·격리, 회귀 테스트의 실패 확인 | **지금 추천.** create-stories/dev-story가 실제로 이 규칙을 참조. 현 test_root도 원본 UE glob의 형태에는 들어감. 폴더 존재·성공은 별개. | `TrackBuilding.` 이름과 `Source/TrackBuilding/Tests`로 예시 정합화. `Private/Tests`로 YAML을 바꾸지 않음. minimal의 테스트 면제와 직접 요청된 테스트를 구별. 회귀 실패 확인은 자동 회귀 테스트를 실제 작성할 때 적용. 무관한 엔진 예제 제거. |
| [scene-files.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/rules/scene-files.md:1), 3–7, 12–41행 | `.tscn/.tres/.unity/.prefab`, Unity `.asset`; Godot UID, Unity GUID/YAML 보존 | **지금 추천, UE용 재작성.** Blueprint·레벨·DataAsset이 필요한 대상에는 바이너리 편집 경계가 중요하지만 원본 규칙에는 UE glob·본문이 모두 없음. | Godot·Unity 절 전부 제외. `.uasset/.umap` 직접 파일·셸 바이트 수정 금지, 에디터 도구 경로·BP 컴파일/패키지 저장 확인·미적용 단계 안내로 교체. 규칙만으로 바이너리 보호가 강제된다고 쓰지 않음. |
| [engine-code.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/rules/engine-code.md:1), 3–5, 10–18, 44–53행 | UE는 `Source/**/Core/**/*.{h,cpp}`; zero allocation, 스레드 경계, 계층, public API 예제·deprecation, RAII·엔진 문서 | **조건부.** 별도 공용 기반/플러그인·핫패스 개선을 다룰 때. 현재 모듈 초기 파일을 Core 엔진 개발로 해석하지 않음. | 성능 문제 측정·스레드 소유 명시·의존 방향은 유지. zero allocation 절대치·모든 API deprecation/마이그레이션 문서·모든 시스템 graceful degradation은 규모별 적용. RAII를 UObject 수명에 일괄 적용하지 않음. §6 참조. |
| [ui-code.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/rules/ui-code.md:1), 3–5, 10–17행 | UE `Source/**/UI/**/*.{h,cpp}`; 상태 소유 금지, 현지화, 양 입력장치, 접근성, 해상도, UI 오디오 | **조건부.** HUD/메뉴 구현 시 추가. 로컬 UI에서 권한 상태를 변경하지 않는 핵심은 지금의 gameplay/network에도 짧게 반영 가능. | `FText`와 로컬 UI/명령 경계 유지. 게임패드·모든 애니메이션 스킵·별도 오디오 이벤트 계층·전체 색약 모드 등은 지원 범위/실제 요구에 맞춰 조건부. 현재 입력은 Keyboard/Mouse. 위젯 BP는 이 C++ glob에 안 잡힘. |
| [data-files.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/rules/data-files.md:1), 3–6, 11–33, 56–64행 | assets/data, data, Unity Data, UE Content/Data의 JSON만; schema, snake_case, C# DTO 설명 | **조건부.** 외부 JSON 반입/내보내기를 정할 때. native DataAsset·DataTable·Config INI에는 현재 원본 glob이 적용되지 않음. | JSON 문법·schema·기본값·호환성 유지. 엔진 불문 snake_case 강제, C# JsonUtility/Newtonsoft/System.Text.Json 예제, UE PascalCase를 잘못 판정할 수 있는 Incorrect 설명 제외. 실제 소비 형식에 맞춰 키 계약 선택. INI·uasset에 JSON 규칙 확대 금지. |
| [shader-code.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/rules/shader-code.md:1), 3–7, 15–50행 | UE `Shaders/**`, `**/*.{usf,ush}`; material prefix·변수·샘플·분기·변형 수·최소 사양 | **조건부.** 커스텀 usf/ush 또는 성능 문제가 있는 Material 작업 시. 일반 BP/Material uasset은 원본 shader glob에 안 잡힘. | UE prefix·의도·파라미터·측정 근거 유지. 모바일 정밀도·다른 엔진 파이프라인·무조건 분기/루프 금지·blur 항상 2pass는 보편 규칙에서 제외. 사용 렌더러/PC 사양을 확인한 뒤 예산 설정. |
| [skill-authoring.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/rules/skill-authoring.md:1), 3–4, 19–94행 | `.claude/skills/**`, `.claude/agents/**`; 미평가 값, permissive default 금지, 생략 고지, 출처, 검사 대상 집합 도출, 실패 관찰 | **조건부, 스킬/검사 이관 시작 때 우선 추천.** 게임 기능의 필수 문서·검사 수를 늘리기 위한 규칙은 아님. | 다섯 의무 유지. hooks/scripts/rules/docs/studio-tests가 원본 glob에 없음을 보완. 출처 경로를 대상 map·5.8.4 소스 확인 절차에 연결. 검사 수정 시만 실패 예제를 검증하며 이번 분석에서 gate를 실행하지 않음. |
| [agent-memory.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/rules/agent-memory.md:1), 3, 8–26, 28–65행 | `.claude/agent-memory/**`; 쓰기 예외 3종, 임시 부재의 영구화 금지, 기억 재확인·쓰기 공개 | **조건부.** 실제 지속 에이전트 메모리 기능을 채택할 때. 현재 최소 경로에는 메모리 디렉터리·세션 복원 기능 자체가 설치되지 않음. | 방법·무효화 조건·기억 재검증 유지. 원본 `production/docs/tests` 쓰기 예외를 대상 `Doc/Source/Config`에 자동 확대하지 않음. 반복된 원본 협업 보일러플레이트는 대상 공통 지침을 참조하도록 축소. |
| [ai-code.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/rules/ai-code.md:1), 3–5, 10–17행 | UE AI 폴더만; AI 2ms, 데이터 파라미터·디버그·BT/utility·편대/측면공격 | **현재 제외.** 현 기획에는 전투 AI·적 NPC·편대 시스템이 없음. 기차 경로·랜덤 배치가 있다는 이유만으로 이 전체 규칙이 필요하지 않음. | 2ms 예산·편대·BT 선호 강제 제외. AI가 추가되면 실제 기능과 측정 예산으로 재검토. 서버 입력 검증은 network가 담당. |
| [narrative.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/rules/narrative.md:1), 3, 8–15행 | `design/narrative/**`; canon, 대사 음성, 세력·비밀 정답·120자 한도 | **현재 제외.** 최소 게임 기능에 세계관/대사 데이터가 없음. UI 텍스트가 생겼다는 이유로 서사 제작 절차 전체를 가져오지 않음. | 120자 제한과 canon 절차 등 현재 불필요. 나중에 실제 서사 문서 위치·화면 규격이 정해지면 필요한 부분만 재검토. |
| [prototype-code.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/rules/prototype-code.md:1), 3, 8–34, 36–68행 | `prototypes/**`; 웹 one-shot config.js/vendor/dist와 throwaway 엔진 실험의 격리·README | **현재 제외.** 대상은 이미 UE 프로젝트이며 Concept stage가 곧 브라우저 prototype 선택을 뜻하지 않음. | 웹 grow/share·vendor/provenance 의존성 제외. 별도 UE spike를 택하면 격리·가설·결과만 추출. `prototypes/`에 놓은 cpp가 자동으로 현재 게임 모듈에 빌드된다고 가정하지 않음. cleanup 문구를 삭제 승인으로 쓰지 않음. |

## 4. frontmatter 범위와 누락 분석

### 4.1 원본 그대로의 결과

현재 `TrackBuilding.h`와 `TrackBuilding.cpp`는 `Gameplay/Core/Network/UI/AI` 하위 폴더에 있지 않아 다섯 코드 규칙 어느 것의 폴더 조건에도 들어가지 않는다. `Config/Default*.ini`와 `TrackBuilding.Build.cs`, `TrackBuilding.uproject`를 직접 대상으로 하는 규칙도 14개에 없다. `Doc/기획.md` 역시 design-docs의 `design/gdd/**` 밖이다.

다음은 **미래 구조를 확정하지 않은 가상 경로**에 대한 패턴 판독이다. 실제 해당 클래스·폴더가 존재한다는 뜻이 아니다.

| 경로 예시 | 원본 패턴 판독 | 제안 |
|---|---|---|
| 실제 `Source/TrackBuilding/TrackBuilding.cpp` | gameplay/engine/network/ui/ai 모두 누락 | gameplay와 network는 모듈 전체 C++에 적용 |
| 가상 `Source/TrackBuilding/Track/TrackActor.cpp` | gameplay와 network 모두 누락 | 동일 파일에 두 규칙 적용 |
| 가상 `Source/TrackBuilding/Gameplay/TrainActor.cpp` | gameplay만 적용, 복제 코드가 있어도 network 누락 | network 폴더와 무관하게 적용 |
| 가상 `Source/TrackBuilding/Network/SessionService.cpp` | network 적용, gameplay는 누락 | 억지 폴더 재배치 없이 파일 내용에 맞춰 두 규칙 사용 |
| 예정 `Source/TrackBuilding/Tests/TrackPlacementTest.cpp` | 원본 test-standards 형태에 포함 | 폴더 부재를 명시하고 향후 YAML test_root 유지 |
| 실제 `Config/DefaultEngine.ini` / `TrackBuilding.uproject` | 14개 규칙의 직접 대상 아님 | network에 설정 변경 절 추가, 엔진/플러그인 변경의 소비자 확인 |
| 가상 `Content/Track/BP_Track.uasset` / `Content/Maps/Test.umap` | scene/gameplay/network 규칙 모두 UE asset 대상으로는 미지정 | scene에 asset 경계 추가, BP 작성자에게 gameplay/network도 명시 읽기 |
| 실제 `Doc/기획.md` | design-docs 누락 | `Doc/**/*.md`로 포괄하되 문서 유형별 기준 분기 |

### 4.2 대상용 paths 제안

아래는 **아직 만들지 않은 대상 규칙 개정안**이다. 원본의 중괄호 확장·대소문자 변형에 의존하지 않도록 확장자를 각각 적는다. 현재 Claude Code/Codex에서 실제 자동 로드가 확인된 패턴이라는 뜻은 아니다.

| 대상 규칙 제안 | `paths` 항목 제안 | 내용 적용 조건 |
|---|---|---|
| `gameplay-code.md` | `Source/TrackBuilding/**/*.h`, `Source/TrackBuilding/**/*.cpp` | 게임 로직에 적용. 테스트 fixture의 의도된 상수·모듈 등록 코드를 튜닝 위반으로 판정하지 않음. |
| `network-code.md` | `Source/TrackBuilding/**/*.h`, `Source/TrackBuilding/**/*.cpp`, `Source/TrackBuilding/TrackBuilding.Build.cs`, `Config/**/*.ini`, `TrackBuilding.uproject` | 모든 C++에서 네트워크 관련 변경을 먼저 식별. INI는 실제 네트워크/온라인/맵 이동 설정을 바꿀 때 본문 적용. |
| `design-docs.md` | `Doc/**/*.md` | 기획/brief/상세 시스템/기술 설계를 구별한 뒤 해당 기준 적용. 기술 설계나 ADR에 GDD 8절 강제 금지. |
| `test-standards.md` | `Source/TrackBuilding/Tests/**/*.h`, `Source/TrackBuilding/Tests/**/*.cpp` | 예정 위치만 지정; 생성·컴파일 가능·테스트 발견 성공을 의미하지 않음. 다른 테스트 루트를 실제 채택하면 그때 함께 변경. |
| `scene-files.md` | `Content/**/*.uasset`, `Content/**/*.umap` | 바이너리를 읽게 유도하는 규칙이 아니라 에디터 경유와 작업 경계 안내. BP/레벨 작업자는 파일 접근 전 명시적으로 읽음. |
| 조건부 `engine-code.md` | `Source/TrackBuilding/**/*.h`, `Source/TrackBuilding/**/*.cpp`, `Source/TrackBuilding/TrackBuilding.Build.cs`, `Source/TrackBuilding.Target.cs`, `Source/TrackBuildingEditor.Target.cs`, `Config/**/*.ini`, `TrackBuilding.uproject` | 공용 기반·스레딩·엔진 통합·설정 변경에 한해 심화 항목 적용. Core 폴더를 새로 만들라는 의미 아님. |
| 조건부 `ui-code.md` | `Source/TrackBuilding/**/*.h`, `Source/TrackBuilding/**/*.cpp` | UI/HUD/Input 관련 코드에만 본문 적용. 위젯 BP는 명시 읽기로 보완. UI 폴더 확정 전 임의 폴더명에 의존하지 않음. |
| 조건부 `data-files.md` | 기존 UE 범위 `Content/**/Data/**/*.json` | 외부 JSON 계약을 선택한 경우만. 다른 실제 JSON 위치를 선택하면 그 확인된 위치를 추가. `.ini/.csv/.uasset`는 별도 형식 규정으로 구분. |
| 조건부 `shader-code.md` | `Shaders/**`, `**/*.usf`, `**/*.ush` | 프로젝트 소유 shader만 검토. 플러그인·vendor 원본이 생기면 해당 소유·변경 경계를 별도로 구분. Material BP는 명시 읽기. |
| 조건부 `skill-authoring.md` | `.claude/skills/**`, `.claude/agents/**`, `.claude/hooks/**`, `.claude/scripts/**`, `.claude/rules/**`, `.claude/docs/**`, `studio-tests/**` | 검사·판정 계약을 작성/수정하는 업무에 적용. 단순 참고 문서 읽기에 전체 gate 실행을 요구하지 않음. |
| 조건부 `agent-memory.md` | `.claude/agent-memory/**` | 실제 메모리 기능을 채택하면 유지. Source/Config/Doc 쓰기 권한으로 확장하지 않음. |

`paths`는 YAML 변수 치환이 아니다. `engine.code_root`를 적는다고 자동으로 glob으로 전개되지 않으므로 이 프로젝트의 확인된 모듈명을 사용하고, 루트 변경 시 규칙·writer·reader를 함께 점검해야 한다. 새 C++ 모듈이나 프로젝트 소유 plugin 모듈이 추가되면 network/gameplay 범위도 함께 갱신한다.

### 4.3 규칙의 로드와 강제는 구별한다

원본 [rules-reference.md 3–35행](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/docs/rules-reference.md:3)은 파일을 **읽을 때** 경로 규칙이 로드되고, 새 파일 생성만으로는 놓칠 수 있으며, 규칙은 강제 장치가 아니라고 기록한다. 대소문자 무시·gitignore 방식도 **Claude Code 2.1.282에서 관찰한 내용**이다. 이를 현재 Codex 또는 모든 클라이언트의 확정 동작으로 일반화하지 않는다.

따라서 최소 구현 writer/리뷰어에는 다음의 **명시 읽기 계약**이 필요하다. 이번에는 실제 스킬을 수정하지 않았다.

1. C++ 새 파일·복제 변경 전 `gameplay-code`와 `network-code`를 읽는다. 폴더명으로 네트워크 여부를 배제하지 않는다.
2. `Doc/` 문서를 만들기 전 `design-docs`, 테스트 작성/해석 전 `test-standards`를 읽는다.
3. Blueprint·레벨·DataAsset 편집 전 `scene-files`를 읽고, 기능에 따라 gameplay/network/ui/shader도 읽는다. `.uasset`의 직접 텍스트 읽기를 로드 트리거로 삼지 않는다.
4. 검증을 원하면 사용 클라이언트에서 읽기와 새 파일 생성 상황을 따로 확인한다. Claude Code 진단은 §5의 `log-instructions.sh`가 후보이며 Codex의 자동 로드를 증명하는 도구가 아니다.

## 5. 실제 동반 파일과 조건부 의존성

규칙 14개에는 실행 frontmatter가 없고, **규칙을 읽는 것만으로 스크립트·스킬·역할이 실행되지 않는다**. 아래는 원문을 유지하거나 관련 워크플로를 채택할 때 생기는 실제 연결이다. 전체 스킬 설치의 전이 의존성 폐쇄집합을 이 보고서가 대신하지는 않는다.

| 묶음·조건 | 확인한 실제 파일 | 이관 판단과 연결 이유 |
|---|---|---|
| 최소 규칙의 기존 입력 | `T/project.yaml`, `T/Doc/기획.md`, `T/.claude/docs/unreal-framework-map.md` | **유지·참조.** 새 사본이나 덮어쓰기가 필요하지 않음. map은 클래스/소유권/복제 근거, 기획은 호스트 종료·서버 선착순 등의 결정 근거. |
| 설정을 동적으로 해석하는 스킬까지 채택 | [config-resolution.md §Resolution chain/Derived lines](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/docs/config-resolution.md:103), [workflow-modes.md 34–49, 123–178행](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/docs/workflow-modes.md:34), [automation-modes.md 50–73행](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/docs/automation-modes.md:50), [yaml-helper.sh 1251–1255행](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/hooks/yaml-helper.sh:1251), [engines.tsv 4행](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/engines/engines.tsv:4) | **소비 스킬 도입 시 함께 연결.** helper가 모드와 provenance를 해석하고 engine row를 읽음. `engine.code_root/test_root`가 엔진 기본 행보다 우선. Bash/Git Bash·필요한 Python 및 셸 유틸리티는 실행 환경 의존성. 규칙 파일만으로 helper 설치를 강제하지 않음. 문서 속 design 경로는 대상 계약에 맞게 개정. |
| minimal brief·스토리 소비자 | [templates/game-brief.md 4–19행](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/docs/templates/game-brief.md:4), [templates/story.md 59–100행](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/docs/templates/story.md:59), [create-stories/SKILL.md 297–324행](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/skills/create-stories/SKILL.md:297), [dev-story/SKILL.md 144–190행](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/skills/dev-story/SKILL.md:144), [story-done/SKILL.md 74–89, 155–169행](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/skills/story-done/SKILL.md:74) | **경로·QA 계약을 같이 바꿀 소비자.** create-stories와 dev-story는 test-standards를 직접 참조. brief의 기계 판독 필드·영문 heading을 임의 번역하면 소비자가 깨질 수 있음. `Doc/기획.md`를 원본 템플릿으로 덮어쓰지 않으며, brief의 최종 위치는 미확정. |
| 상세 GDD를 자발적으로 추가할 때 | [templates/game-design-document.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/docs/templates/game-design-document.md:1), [templates/guidance/game-design-document-guide.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/docs/templates/guidance/game-design-document-guide.md:1), [design-system/references/3-file-skeleton.md 29행](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/skills/design-system/references/3-file-skeleton.md:29), [4-section-design.md 22–29행](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/skills/design-system/references/4-section-design.md:22), [design-review/SKILL.md 139–164행](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/skills/design-review/SKILL.md:139), [gdd-structure-check.sh 8–22, 42–58, 129–140행](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/scripts/gdd-structure-check.sh:8) | **현재 최소 묶음에서는 제외.** GDD를 채택할 때 작성자·템플릿·리뷰어·제목 검사를 함께 조정해야 함. 검사 script는 존재/placeholder 관찰만 제공하고 tier 판정은 호출자가 담당. 기본 탐색은 `design/gdd`, 제목은 영문. 한글 제목을 채택한다면 별칭 지원도 필요. |
| UE 코드·자산 담당 역할을 가져올 때 | [gameplay-programmer.md 99–115행](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/gameplay-programmer.md:99), [network-programmer.md 64–92행](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/network-programmer.md:64), [unreal-specialist.md 71–94, 111–122행](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/unreal-specialist.md:71), [ue-replication-specialist.md 70–131행](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/ue-replication-specialist.md:70), [ue-blueprint-specialist.md 70–89행](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/ue-blueprint-specialist.md:70) | **규칙 설치의 필수 역할은 아님.** 역할을 채택하면 중복 기준을 함께 고침. gameplay의 무조건 테스트·네트워킹 위임, network의 prediction/migration, Unreal의 prediction 의무가 개정 규칙과 다르면 다시 충돌. 스토리 성격/해석된 specialists 값으로 선택하고 팀 전체를 통복사하지 않음. |
| 동적 specialist·리뷰 분기 | [config-resolution.md 235–238행](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/docs/config-resolution.md:235), [dev-story/SKILL.md 139–153행](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/skills/dev-story/SKILL.md:139), [ue-umg-specialist.md 61–88행](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/ue-umg-specialist.md:61) | `specialists`는 engine 및 실제 설정으로 해석; 현재 대상에 명시 블록이나 legacy 파일이 없으므로 추가 전문 역할이 설정됐다고 가정하지 않음. UI/shader 역할은 해당 변경 때만. `solo/individual`을 유지하고 director gate·다수 역할·CommonUI/GAS를 자동 필수화하지 않음. |
| 실제 Automation 기반을 설치할 때 | [test-scaffold.sh 345–391행](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/scripts/test-scaffold.sh:345), [test-setup/references/3-directories.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/skills/test-setup/references/3-directories.md:1), [qa-tester.md 66–73행](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/agents/qa-tester.md:66), [test-evidence-review/SKILL.md 245–265행](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/skills/test-evidence-review/SKILL.md:245) | **조건부.** scaffold/README의 `Source/<Module>/Private/Tests` 예시를 실제 YAML test_root에 맞춤. test-standards의 명명법만으로 test-setup·CI·qa-tester·전체 QA 역할을 설치할 이유는 없음. 0개 발견/미실행과 성공을 구별하고 waived와 미평가를 별개로 유지. |
| JSON 자동 문법 확인을 선택할 때 | [validate-assets.sh 56–59, 92–146, 160–166행](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/hooks/validate-assets.sh:56), [settings.json 238–250행](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/settings.json:238), [validate-commit.sh 495–610행](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/hooks/validate-commit.sh:495) | **조건부.** PostToolUse 등록과 Bash/Python이 필요; helper는 경고 전달에 사용. 실제 hook은 assets/Assets/Content의 Data JSON을 검사하며 규칙의 루트 `data/**/*.json`과 범위가 같지 않음. schema·참조 무결성·INI·DataAsset은 확인하지 않음. 쓰기 후 검사이므로 쓰기 자체를 막은 것으로 보고하지 않음. commit 검사까지 선택하면 별도 의존성 검토 필요. |
| 규칙 로드 진단을 선택할 때 | [rules-reference.md 27–35행](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/docs/rules-reference.md:27), [log-instructions.sh 32–71, 82행](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/hooks/log-instructions.sh:32) | **진단용 조건부.** `InstructionsLoaded` 이벤트에 별도 등록해야 함. 원본 settings의 hooks에는 이 이벤트 등록이 없어 파일 존재만으로 작동하지 않음. 출력은 `production/session-logs/instructions-loaded.log`; 게임 실행 결과나 강제 검증 증거는 아님. |
| agent memory·세션 복원을 선택할 때 | [agent-memory.md 8–26행](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/rules/agent-memory.md:8), [원본 .gitignore 18–21행](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.gitignore:18), [session-start.sh 291행](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/hooks/session-start.sh:291), [write-guard.sh 375행](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/scripts/write-guard.sh:375) | **조건부이며 서로 다른 기능.** 메모리의 내용 규칙이 체크포인트 훅 설치를 요구하지 않음. 메모리·세션 상태의 ignore와 실제 저장 경로·클라이언트 지원을 따로 채택. source/config 쓰기 예외 확대 금지. |
| prototype를 별도로 택할 때 | [prototype-code.md 20–34행](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/rules/prototype-code.md:20), [one-shot-share.sh 13–14, 50–79행](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/scripts/one-shot-share.sh:13), [prototype/references/spike-mode.md](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/skills/prototype/references/spike-mode.md:1) | **현재 제외.** share는 index.html에서 dist를 만드는 웹용 script. UE 규칙 의존성으로 가져오지 않음. 별도 spike에만 필요한 실험 계약을 선택. |

## 6. Unreal용 변경 명세와 소비자 영향

### 6.1 설계 문서: 경로 정합성과 minimal 유지

**위치:** `S/.claude/rules/design-docs.md:3,8–21`; `S/.claude/docs/workflow-modes.md:142–178`; `S/.claude/skills/create-stories/SKILL.md:297–324`.

**현재 문제:** 원본은 `design/gdd`만 match하면서 minimal의 기준은 다른 위치 `design/game-brief.md`라고 안내한다. 대상 기준은 `Doc/기획.md`다. glob만 `Doc/**`로 바꾸면 기술 설계·ADR까지 같은 GDD 형식 검사를 받기 쉽다.

**변경안:** `Doc/**/*.md`에서 문서 유형을 식별한다. 기존 기획은 원문으로 유지하고, 구현할 최소 범위·순서·검증 가능한 수용 기준을 연결한다. optional 상세 GDD에서는 정의한 수치의 변수·범위·예시, 구체적 예외를 요구한다. `Detailed Rules`와 `Detailed Design` 별칭은 유지한다. `collaborative`의 향후 절별 설계 협업을 유지하되 이미 승인된 보고서·수정 범위를 매 절마다 재승인하도록 확대하지 않는다.

**소비자 영향:** brief 최종 위치는 아직 미확정이다. 권장 후보는 `Doc/` 아래 별도 brief이며 **기존 `Doc/기획.md`를 brief 템플릿으로 덮어쓰는 안은 추천하지 않는다**. 위치를 채택한 뒤 brainstorm/create-stories/dev-story/story-done, workflow-modes/catalog, brief 검사·다음 작업 안내 등의 경로를 함께 변경해야 한다. 이번에는 위치를 확정하거나 해당 파일을 생성하지 않았다. 한글 제목/영문 기계 판독 키의 계약도 함께 결정한다.

### 6.2 네트워크: 기능별 코드 전체와 리슨 서버의 두 실행 경로

**위치:** `S/.claude/rules/network-code.md:3–17`; `T/Doc/기획.md:138–155`; `T/.claude/docs/unreal-framework-map.md:12–28,43–53,62–69`.

**현재 문제:** 폴더 기준 누락, 모든 메시지 버전·모든 동작 rollback 강제, 호스트 이전 요구가 있다. `reliable/unreliable`을 모든 복제 값에 일괄 지정하라는 문장은 프로퍼티 복제와 RPC 전달 방식을 혼동시킬 수 있다.

**변경안 제안:**

- 서버가 획득·배치·회수·연결·승패를 확정한다. 같은 조각·같은 위치의 경쟁은 기획의 서버 기준 선착순을 따른다. 허용 거리·현재 소지·조각 유효성·배치 가능 상태·소유권·빈도 등 **실제 동작의 검증 항목**을 기록한다.
- 클라이언트 요청은 Framework Map의 소유 PlayerController/Pawn/Component 경로를 따른다. 공유 월드 Actor에 소유 연결이 있다고 가정하지 않는다.
- 상태 소유자, 누구에게 복제되는지, relevancy/조건/갱신 기준을 적고 RPC 이벤트에는 별도로 전달 보장·빈도·중복/유실 대응을 정한다. multicast를 현재 상태의 유일한 기록으로 삼지 않는다.
- C++ RepNotify는 map 기록상 호스트에서 자동 실행되지 않으므로 서버 상태 변경 뒤 호스트 표시 갱신과 클라이언트 OnRep 갱신이 모두 같은 결과를 내도록 설계·검증한다. UI에서 GameMode를 공유 상태 저장소로 쓰지 않는다.
- **호스트 종료 시 게임 종료**를 유지한다. host migration은 현재 이관 규칙에서 제외한다. 재접속·중도 입장은 허용 여부가 미확정이므로 구현 의무로 쓰지 않는다. 종료/연결 실패 시 클라이언트 안내·복귀 상태는 구체화할 후속 질문이다.
- prediction/rollback은 필요한 동작에만 설계한다. 입력 반응용 배치 미리보기와 서버 확정 상태를 구별하고, 캐릭터 이동의 엔진 기능을 먼저 검토한다. 새 범용 rollback 시스템을 필수 의존성으로 만들지 않는다.
- 메시지 버전은 커스텀 직렬화/세이브/접속 호환 계약을 실제 만들 때 적용한다. 엔진이 담당하는 모든 RPC에 별도 수동 버전 필드를 자동 요구하지 않는다. 로그 rate limit은 유지하고 대역폭 수치·갱신 빈도는 측정 전 제안/미확정으로 표시한다.

**소비자 영향:** network-programmer의 86–92행, unreal-specialist의 111–116행, ue-replication-specialist의 예측·relevancy·대역폭 조항을 함께 조정해야 한다. gameplay-programmer의 “네트워크는 무조건 다른 역할에게 위임”도 기능 단위 안에 코드가 섞이는 대상 및 `individual` 운영과 조율한다. 현재 규칙을 바꾼다고 전체 역할 체계를 설치하지 않는다.

### 6.3 C++·Blueprint·설정·수치 경계

**위치:** `S/.claude/rules/gameplay-code.md:11–19`; `engine-code.md:10–18`; `data-files.md:11–30`; `T/.claude/docs/unreal-framework-map.md:19–28`.

**변경안:** 조정할 게임 수치는 DataAsset/DataTable/편집 가능한 설정으로 노출하고 의미·기본값·유효 범위를 필요한 수준으로 기록한다. 구조상 고정된 계산 상수나 테스트 경계값까지 외부 JSON으로 옮기지 않는다. C++는 게임 판정·네트워크 경계를, BP는 자산 구성·수치 조정을 주로 맡긴다는 현재 전제를 유지한다. 시간 기반 계산에는 적절한 시간 단위를 쓰되 이미 시간 처리를 담당하는 API 호출에 DeltaTime을 중복 적용하도록 유도하지 않는다.

공용 기반 규칙을 추가하면 측정된 핫패스의 불필요 할당·Tick·스레드 경계를 검토하고, 엔진 클래스·Subsystem 사용을 일괄 singleton 금지나 의존성 주입 강제로 막지 않는다. RAII·스마트 포인터는 자원 유형에 맞춰 쓰며 UObject 참조/GC 수명은 Unreal 방식으로 구별한다. 원본 Unreal specialist도 72–80행에서 UObject와 non-UObject 포인터를 구별한다.

`Config/**/*.ini`, `.uproject`, `Build.cs`는 JSON 데이터 규칙이나 C# 규칙으로 처리하지 않는다. 변경한 설정의 실제 소비 모듈·플러그인·기본 맵·세션 영향과 재시작/실행 확인 필요성을 기록한다. 현재 Build.cs의 온라인 기능 주석과 uproject의 plugin 목록만으로 Steam 세션 구현이 완료됐다고 판단할 수 없다.

**소비자 영향:** gameplay/engine/network 역할과 dev-story의 189–190행 “game data file” 지침을 UE 형식에 맞춰야 한다. data-files를 추가해도 기존 JSON이 없는 대상에 JSON mapping 계층을 먼저 만들 필요는 없다. UI의 gamepad/CommonUI·shader의 모바일 예산·AI의 2ms를 설정에 임의 추가하지 않는다.

### 6.4 테스트 요구·미실행·호스트/클라이언트 증거

**위치:** `S/.claude/rules/gameplay-code.md:17`; `test-standards.md:16–46,90–113`; `S/.claude/skills/dev-story/SKILL.md:172–181`; `story-done/SKILL.md:79–89`; `T/CLAUDE.md:41–45`.

**현재 문제:** 원본 gameplay와 gameplay-programmer는 무조건 테스트를 요구하지만 minimal dev-story는 Logic/Integration 테스트를 작성하지 말라고 명시한다. test-standards의 UE 예시 위치는 `Private/Tests`이고 대상 YAML은 모듈 안의 `Tests`다.

**변경안:** “자동 테스트의 필요 여부는 `qa.level`·명시된 작업 범위로 결정하고, 작성하는 테스트에는 이 규칙을 적용”으로 정리한다. 순수 트랙 연결/배치 검증 등 실제 테스트 가치가 있는 로직은 후보로 제시할 수 있으나 minimal을 standard로 올리지 않는다. 직접 요청한 테스트까지 면제하지 않는다. 버그 수정에 자동 회귀 테스트를 작성할 때는 수정 전 실패·수정 후 통과의 근거를 남긴다. 위험한 상태 복원을 위해 현재 작업 트리를 무단 덮어쓰지 않는다.

예시는 예정 `Source/TrackBuilding/Tests`와 `TrackBuilding.[System].[Scenario]`, 고유한 `F…Test` 클래스로 맞춘다. `TrackBuilding2.`를 필터로 쓰지 않는다. test_root가 아직 없는 현 상태를 “0 tests passed”로 바꾸어 표현하지 않는다. 명령 존재·정적 경로 확인·단순 종료 코드만으로 성공을 주장하지 않는다.

네트워크 완료 기준은 적어도 호스트와 클라이언트 각각의 행동·관찰 결과를 구별한다. 호스트만 확인한 PIE/스크린샷, headless smoke, 순수 계산 Automation은 2인 Steam 플레이 검증을 대신하지 않는다. 실제 실행 전에는 `미실행/미평가`, minimal 면제는 `면제`, 실행 실패는 `실패`로 구분한다. 기존 대상 지침의 양쪽 화면 확인을 유지하되 이번 세션에는 어느 실행도 하지 않았다.

**소비자 영향:** test-standards, gameplay 역할, templates/story의 Test Evidence, create-stories/dev-story/story-done, qa-tester/test-evidence-review, test-scaffold의 경로·이름·면제 기준이 일치해야 한다. 현재 minimal에는 test-scaffold·CI 전체 설치를 강제하지 않는다.

### 6.5 바이너리 자산과 settings.json의 분리

**위치:** `S/.claude/rules/scene-files.md` 전체; `S/.claude/docs/rules-reference.md:53,67–68`; `S/.claude/settings.json:120–121`; `S/.claude/agents/unreal-specialist.md:88–94`.

**현재 문제:** 원본 scene rule은 Godot/Unity 전용이다. 원본은 `.uasset/.umap` 보호를 settings의 `Edit(**/*.uasset)`, `Edit(**/*.umap)` deny에 맡기지만 대상 settings는 없다. 해당 deny는 표현상 **Edit 도구 규칙**이며 모든 셸·Write·다른 클라이언트의 경로까지 막는 완전한 장치로 간주할 수 없다.

**변경안:** 대상 scene rule에 직접 바이트 편집 금지, 가능한 에디터 도구/스크립팅/MCP/commandlet, BP 컴파일·저장·참조 확인, 접근 수단이 없을 때 미적용 editor 단계 안내를 명시한다. 도구가 실제로 가능한지 확인하지 않고 “편집기 도구가 없다” 또는 “항상 있다”고 고정하지 않는다. 향후 자산 이동/이름 변경은 에디터의 참조 보존 절차를 따르게 한다.

**소비자 영향:** unreal-specialist와 ue-blueprint-specialist의 바이너리 절을 공통 개정 규칙과 일치시킨다. 현재 `scene-files` 개정을 위해 **원본 settings.json 전체를 복사하지 않는다**. 필요한 deny·개별 hook은 별도 설정 단위의 검토 대상이며, 실제 사용하는 클라이언트의 적용 여부를 확인한 후 필요한 항목만 구성한다. 원본의 권한·statusLine·세션 훅·commit/push 훅을 규칙 의존성으로 통설치하지 않는다.

### 6.6 엔진 참고 자료와 운영 규칙

**위치:** `S/.claude/rules/engine-code.md:18`, `skill-authoring.md:57–65`; `S/docs/engine-reference/unreal/VERSION.md:5–9,21–28,36,73`; `T/CLAUDE.md:34–39`.

원본 engine reference는 5.8 pin/설치 버전 `NOT DETERMINED`, 최신 hotfix 5.8.3 및 Iris 상태 등을 서술한다. 대상 map은 로컬 5.8.4 소스 근거와 legacy/Iris 조건을 별도로 기록한다. **같은 “UE 5.8” 문자열이라고 일괄 신뢰하거나 대상을 원본 문서로 덮어쓰지 않는다.**

대상용 엔진 규칙은 우선 `project.yaml → unreal-framework-map → 부족한 부분의 설치 5.8.4 소스 확인/경로 기록 → 확인 불가 표기`로 연결하는 편을 추천한다. 원본 `docs/engine-reference/unreal/**`를 추가할 경우 실제 필요한 문서별로 사실·버전을 재검증하고 참고 범위를 표시해야 한다. 기존 map은 보존하며 원본 technical-preferences를 대체본으로 복사하지 않는다. 대상에 현재 없는 legacy 파일도 이번 최소 규칙의 필수 생성물이 아니다.

skill-authoring을 추가하면 `NOT ASSESSED`가 pass보다 우선하고 알려진 실패를 가리지 않도록 판정 어휘를 맞춘다. agent-memory를 추가하면 “테스트 폴더가 없다/게임이 없다”를 영구 사실로 저장하지 않고 확인일·무효화 조건·재확인 방법을 기록한다. 메모리 규칙의 권한 예외를 현재 프로젝트 공통 지침의 재작성 근거로 사용하지 않는다.

## 7. 이관 순서와 완료 확인 기준

아래는 **향후 실제 이관 때의 순서 제안**이다. 이번 분석 작업의 수행 결과와 구별한다.

| 순서 | 작업 제안 | 완료 확인 기준 |
|---|---|---|
| 1 | 경로·모드 계약 고정 | 실제 이름 `TrackBuilding`, code_root/test_root 및 여섯 파생값 유지 확인. `Doc/기획.md` 보존. brief 최종 경로는 채택 전에 미확정으로 표시. |
| 2 | 최소 5개 규칙의 대상용 개정본 작성 | 14개 판정과 제외 이유 기록 유지. GDD 8절·모든 로직 테스트·host migration·범용 rollback을 현재 필수로 요구하지 않음. 원본 파일은 보존. |
| 3 | 기존 입력 참조·명시 읽기 연결 | 새 C++/Doc/BP/테스트 작성 시 관련 규칙을 읽는 소비자 연결이 있음. `Network` 폴더 밖의 복제 코드도 포함. 존재하지 않는 technical-preferences/engine-reference를 필수 입력으로 남기지 않음. |
| 4 | 필요한 경우에만 소비 스킬·역할·script와 조정 | §5의 조건에 따라 활성 경로만 연결. settings 전체 복사·팀 전체 설치 금지. test-scaffold/GDD checker를 채택했다면 target root·언어·형식이 writer/reader와 일치. |
| 5 | 정적 적용 범위와 클라이언트 로드 확인 | 모듈 루트·가상 Track/Gameplay 안의 네트워크 코드·Config·Doc·BP 새 자산 작업 등 대표 사례를 확인. 단순 glob 판독과 실제 클라이언트 로드 결과를 구별. 새 파일만 만드는 경우도 확인. |
| 6 | 구현/실행 검증을 별도 수행 | 실제 구현 후 필요 범위의 빌드/Automation/에디터 자산 확인 및 호스트·클라이언트 관찰 결과를 남김. 테스트 면제·미실행·미발견·실패·통과를 구별. 실행 승인·범위는 해당 후속 작업 기준이며 이번 보고서가 수행했다고 주장하지 않음. |

### 미확정 사항

- minimal brief의 최종 `Doc/` 경로와 기존 기획과의 참조 관계. 기존 기획을 덮어쓰는 결정은 내려지지 않았다.
- 기능별 C++ 하위 폴더, 테스트 실제 파일 배치, 공용 기반 폴더·추가 모듈/플러그인 계획. 현재 제안은 임의의 `Gameplay/Network/Core` 생성을 전제하지 않는다.
- 중도 입장·재접속 허용 여부와 호스트 종료/세션 실패 때의 UI·복귀 동작. 호스트 이전은 현 기획 기준 제외.
- Steam OnlineSubsystem/Online Services 선택과 실제 세션 구현·설정. Framework Map의 판단 예시는 구현 완료 증거가 아니다.
- latency·대역폭·frame/AI/shader 예산, 최소 PC 사양, gamepad·현지화·접근성 기능의 구체 범위. 원본 수치·기능을 자동 채택하지 않는다.
- BP·Material·DataAsset을 다룰 실제 에디터 도구와 저장/컴파일 관찰 방식, 사용하는 클라이언트의 규칙 자동 로드·deny 지원.
- `technical-preferences.md` 부재가 의도된 제거인지 여부. 현 YAML과 Framework Map으로 확인되는 정보는 유지하고, 기존 참고 문서의 파일 존재 설명만 현재 관찰과 구별했다.

## 8. 이번 보고서의 완료 상태

- 원본 규칙 **14/14개**의 본문과 frontmatter를 읽고 파일별 판정·근거 행·현재 필요성·대상 변경안을 작성했다.
- 설정·실제 프로젝트/모듈명·Doc 경로·테스트 예정 경로·누락 legacy 파일을 대조했다.
- 관련 원본 SKILL.md는 **분석 자료로만 읽었으며 실행하지 않았다**. 의존 script 역시 읽기만 했다.
- 이번 세션의 저장 대상은 **이 보고서 하나**다. 다른 분석 보고서·색인·sessions.json·공통 지침·원본 파일을 수정하지 않았다.
- 게임 구현·빌드·Automation·2인 연결·플레이·자산 컴파일 성공 여부는 **미평가**다. 문서 작성 완료와 게임 검증 완료를 구별한다.
