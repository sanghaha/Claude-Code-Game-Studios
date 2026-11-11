# TrackBuilding2 선택 이관 검토 02 — 기획·설계 절차

작성일: 2026-11-11 (Asia/Seoul). 분석 기준: CCGS v1.3.0, 커밋 `abc0de79d6ceb0b5d7aa693945849b3937afbe6a`.

## 1. 범위와 확인한 전제

- 원본 **S**: [C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios). 아래 `S/…`는 이 루트에 실제 존재하는 파일을 뜻한다.
- 대상 **T**: [C:/Users/Sangha_Remote/Documents/TrackBuilding2](C:/Users/Sangha_Remote/Documents/TrackBuilding2). 아래 `T/…`는 대상 경로이며, 새 산출물 경로는 반드시 **예정**으로 구분한다.
- 관할: 요청한 12개 스킬과 연관 UX 작성·리뷰 2개, **14개 폴더의 실제 파일 65개**. `SKILL.md`, `references/`, 유일한 `architecture-decision/CONTRACT.md`를 모두 읽고 §3에서 개별 판정했다. `team-ui`는 UX 소비자 경계만 추가 확인했다. 원본 스킬을 분석 자료로 읽었으며 실행하지 않았다.
- 원본·대상 루트, 그 상위 `C:/`, `C:/Users/`, 사용자 홈, `Documents/`와 두 저장소 내부에서 적용할 `AGENTS.md`를 찾지 못했다. 이 채팅의 범위 제한과 대상 `CLAUDE.md`를 따랐다. 프로젝트 공통 지침은 재작성하지 않는다.
- 원본 HEAD가 기준 커밋과 같음을 확인했다. 추적 파일의 변경은 관찰되지 않았고 기존 `ax-setup/`은 미추적 상태였다. 대상에는 미추적 설정·문서가 있으므로 이를 기존 작업으로 취급한다.

대상 사실은 다음과 같다.

| 항목 | 확인 내용·근거 |
|---|---|
| 기획 기준 | `T/Doc/기획.md` 유지. 2인 협동·Steam 리슨 서버·트랙 빌딩, 3분 제한, 준비 30초/주행 10초/과열 10초, 서버 기준 선착순 판정. `기획.md:7–19, 25–61, 138–155` |
| 이름·스택 | 작업 폴더만 TrackBuilding2. `.uproject`, 모듈, 타깃은 TrackBuilding. UE 5.8.4, C++ 기반+Blueprint, PC. `T/CLAUDE.md:3–20`, `T/project.yaml:6–29` |
| 고정 설정 | `Concept / hobby / minimal / collaborative`. 여섯 파생값은 workflow=minimal, density=terse, QA=minimal, story_granularity=coarse, review=solo, team=individual. 설정을 추가·변경하지 않는다. `T/project.yaml:6–16`; 해석 근거는 `S/.claude/docs/workflow-modes.md:34–48, 111–119` 및 기존 설정 분석 |
| 문서 배치·엔진 근거 | 기획·설계는 `Doc/`; 클래스·소유권·복제·RPC 설계 전에 기존 `.claude/docs/unreal-framework-map.md`를 읽는다. 지도에 없는 엔진 사실은 설치된 5.8.4 소스에서 확인하거나 미확정으로 둔다. `T/CLAUDE.md:26–39` |
| 기술 설정 문서 차이 | 요청에 읽기 자료로 이름이 나온 `T/.claude/docs/technical-preferences.md`는 **이번 실파일 확인에서는 없음**. 기존 `ccgs-settings-skill-impact.md:24`의 “있음” 서술과 다르다. 이를 복구·생성하지 않았으며 `project.yaml`과 프레임워크 맵을 현재 근거로 사용했다. 없는 허용 라이브러리·성능 예산은 추정하지 않는다. |
| 구현·검증 | `T/Source/TrackBuilding/`에는 `TrackBuilding.cpp`, `.h`, `.Build.cs`가 있으며 모듈 등록·의존 설정 수준이다. 게임 동작 구현은 확인되지 않았다. 테스트 루트 `Source/TrackBuilding/Tests`는 예정이고 폴더가 없다. 빌드·Automation·2인 검증 성공은 확인되지 않았다. `TrackBuilding.cpp:3–6`, `.Build.cs:5–21`, `project.yaml:24–25, 37–44` |

기존 `S/ax-setup/ccgs-adoption-reference.md`와 `T/ax-setup/ccgs-settings-skill-impact.md/.json`은 참조하되 원본과 대조했다. 전자의 TrackBuilding용 `REQ-*`, 기존 설계·ADR, 변경 영향 확대 보류는 **TrackBuilding2의 결정이 아니다**(`ccgs-adoption-reference.md:161–163, 455–462, 471–491`). 후자의 JSON도 스스로 정적 참조 색인이며 의존성 폐쇄가 아니라고 명시한다(`method`).

이 문서의 **추천은 아직 이관 확정이 아니다**. “제외”는 현재 이관 목록에서 빼라는 뜻이다. 원본 삭제, 스킬·역할·규칙·훅 설치, 설정 수정, 엔진 실행·빌드·테스트, 커밋을 하지 않았고 이 보고서 하나만 저장한다.

## 2. 지금 추천하는 경로와 최소 묶음

### 2.1 선택 판단

**우선 추천은 기존 기획 → 짧은 실행용 brief → coarse 스토리 → 구현으로 이어지는 경로다.** 전체 GDD·아키텍처·TR registry·manifest를 시작 조건으로 추가하지 않는다. 원본도 minimal에서는 brief와 엔진만 선행 조건으로 삼고 상세 스킬은 자발적으로 쓸 수 있다고 한다(`workflow-modes.md:123–156`). 기존 기획에 이미 게임 방향이 있으므로 새 콘셉트를 다시 선택하게 하는 brainstorm 원형은 그대로 가져오지 않는다.

| 스킬 | 이번 추천 | 현재 최소 경로에서의 필요성·추가 시점 |
|---|---|---|
| `brainstorm` | **추천, 기존 기획 입력 모드로 변경** | 최소 brief 작성기를 재사용하는 후보. 신규 아이디어 질문을 생략하고 `Doc/기획.md`에서 추출한다. 스킬 설치 자체가 필수는 아니며 같은 계약으로 brief를 작성해도 된다. |
| `map-systems` | 조건부, 초기 의존 검토에 유용 | 트랙·획득/컨베이어·기차/라운드·랜덤 지형·세션·HUD의 연결을 한 번 정리할 때. 전체 시스템 목록과 우선순위를 별도 유지할 필요가 있을 때만 파일화. minimal 선행 필수 아님. |
| `quick-design` | 조건부 우선 후보 | 동작이 정해진 작은 추가·변경을 짧게 명세할 때. 현재 구현이 없는 전체 트랙/네트워크 시스템을 “작은 튜닝”으로 분류하지 않는다. |
| `design-system` | 조건부, 위험 기능 상세화 | 동시 획득·배치 경쟁, 연결 판정, 기차 경로 등 추측이 구현 오류로 이어지는 기능을 선택해 상세 기획. minimal에서 선택 GDD는 Summary+5개 절, terse 유지. |
| `architecture-decision` | 조건부, 구현 선택 직전에 권장 | 복제 단위·상태 소유권·Steam 세션 API 등 실제 대안 선택이 생길 때. 전체 master architecture 없이도 대상 근거로 단일 결정을 기록할 수 있게 수정. |
| `create-architecture` | 조건부, 여러 기능의 소유권 충돌 시 | 모든 문서를 만드는 관문으로 쓰지 않는다. `layers`/`data-flow`/`api-boundaries` 등 필요한 범위만 선택. |
| `design-review` | 조건부 | 실제 GDD가 생긴 뒤 독립 검토. minimal에서는 구조 누락을 자문으로 보고 기존 기획을 8절 GDD로 강제하지 않는다. |
| `architecture-review` | 조건부 | 실제 ADR·상세 요구사항이 생긴 뒤 범위를 정해 검토. 원형의 minimal “not applicable”와 문서 부재 관문을 조정해야 선택 ADR 검토가 가능하다. |
| `create-control-manifest` | 현재 묶음에서 제외, ADR 누적 후 조건부 | 현재 Accepted ADR가 없으므로 추출할 규칙이 없다. 소수 ADR와 기존 프레임워크 맵의 중복 비용이 크다. 필요해지면 출처 있는 파생 규칙표로 생성. |
| `review-all-gdds` | 현재 묶음에서 제외, 2개 이상 GDD 후 조건부 | 기획 하나·빈 GDD로 교차 검토할 수 없다. 원형은 solo와 무관하게 두 game-designer 작업을 병렬 위임하므로 비용·실행 모델도 선택해야 한다. |
| `propagate-design-change` | **현재는 조건부 대기, 기능 영구 제외 아님** | 지금 ADR/GDD가 없어서 영향 대상이 없다. 선택 ADR가 생기면 minimal이어도 명시 연결된 결정을 확인하는 수정안을 권장한다. TrackBuilding의 보류를 승계한 판단이 아니다. |
| `reverse-document` | 현재 묶음에서 제외 | 기본 모듈 코드를 게임 구현으로 역추론하면 안 된다. 문서 없는 실제 동작이 누적된 후 출처를 붙여 사용. 브라우저 brief 분기는 Unreal 범위에서 제외. |
| `ux-design` | 조건부, HUD·초대·상호작용 흐름 전 | 원본 전체 UI 조직 대신 HUD 또는 특정 흐름을 선택. 상태·데이터 소유자·서버 거절 피드백이 필요할 때 먼저 명세. |
| `ux-review` | 조건부, UX 명세 작성 후 | 읽기 전용 품질 검토. 최소 경로 전체의 상시 관문으로 만들지 않는다. 접근성 기준 미정은 NOT ASSESSED로 분리. |

### 2.2 이관 묶음

| 묶음 | 내용 | 추가하지 않는 전제 |
|---|---|---|
| **M0 — 지금 최소 추천** | 수정한 `brainstorm/SKILL.md` 1개, `.claude/docs/templates/game-brief.md`, 설정 해석용 `yaml-helper.sh`, 해당 공통 계약 3문서(config-resolution/automation-modes/workflow-modes). 기존 `Doc/기획.md`, `project.yaml`, 프레임워크 맵을 읽기 입력으로 연결 | full-concept 참조·director 역할·레지스트리·GDD·ADR·manifest·단계 게이트·hooks 등록을 요구하지 않음. 호출하지 않는 분기와 참조도 함께 제거해야 이 묶음이 성립 |
| M1 — 작은 변경 | 수정한 `quick-design/SKILL.md`와 M0의 설정 계약 | GDD가 없어도 기획의 해당 절과 실제 스토리를 입력으로 받음. 스펙→스토리 연결 수정은 필요 |
| D — 선택 상세 기획 | `map-systems`, `design-system`, `design-review`의 선택 기능에 필요한 파일·템플릿·스크립트 (§3·4) | 설정을 standard/full로 올리지 않음. `map-systems`는 선택이고 시스템별 GDD 작성도 개별 선택 |
| A — 기술 결정 | `architecture-decision` 폴더 전체와 ADR 양식·상태 helper·선택 registry, Unreal 근거. 필요할 때 `create-architecture`·`architecture-review` 추가 | 선택 ADR 작성만을 위해 전체 TR/manifest/스프린트를 도입하지 않음. 원형 유지 시 엔진 specialist 위임은 solo에서도 남음 |
| U — 필요한 UX | `ux-design`, `ux-review`와 선택 모드의 양식·가이드 | HUD 하나를 위해 pattern library·player journey·전체 화면 명세·team-ui를 일괄 필수화하지 않음 |
| C — 변경 관리 | 실제 상세 기획/ADR 연결이 생겼을 때 수정한 `propagate-design-change`와 `adr-status.sh`, `md-section.sh` | 코드 자동 수정·대체 ADR 자동 채택 기능으로 확장하지 않음 |

M0는 **기획 영역의 최소 묶음**이다. `create-stories → dev-story → story-done` 구현 흐름 전체가 이 묶음만으로 설치되는 것은 아니다. 구현 담당 이관과 brief 경로·스토리 필드를 함께 맞춰야 실제 사용 가능하다. 원본을 분기 수정 없이 그대로 보존하려면 삭제한 참조의 파일까지 가져와야 하므로 “M0만 복사 후 원형 그대로 실행”은 성립하지 않는다.

## 3. 관할 65개 파일의 개별 판정

표의 경로는 모두 `S/.claude/skills/` 기준이다. **최소**는 M0에 포함, **선택**은 해당 기능 채택 시 포함, **확장**은 현재 minimal 묶음에서는 제외하고 특정 분기를 유지할 때만 포함, **현재 제외**는 지금 쓸 입력이 없는 파일이다. “선택” 파일도 소유 스킬이 참조하면 함께 이관하거나 호출을 수정해야 한다.

### 3.1 콘셉트·시스템 분해·간단 설계 — 4개

| 파일 | 판정 | 근거·필요성 |
|---|---|---|
| `brainstorm/SKILL.md` | 최소, 수정 | `:21–29, 41–46, 48–51, 88–108`. minimal brief만 작성. 기존 기획 입력·경로·다음 단계 수정 후 사용. |
| `brainstorm/references/full-concept.md` | 확장 | `§Phase 1–8`, 특히 `:7–8, 74–108, 119–167, 175–194`. 발견 질문·pillar·시장·director와 full concept 전용. 최소 실행에서 읽지 말라는 SKILL 지시가 있음. |
| `map-systems/SKILL.md` | 선택 | `:66–91, 288–345, 350–383`. minimal에서는 인덱스 불필수. 기존 기획을 읽도록 변경하고 선택적으로 목록·의존·우선순위 저장. gate 및 active.md 연결도 포함. |
| `quick-design/SKILL.md` | 선택 | `:16–55, 80–94, 242–306`. 일주일 이하의 작은 변경. 자체 양식 포함, minimal의 brief 대체 입력 있음. JSON 수치 파일 전제와 스토리 참조 연결 수정. |

### 3.2 상세 기획 — 10개

| 파일 | 판정 | 근거·필요성 |
|---|---|---|
| `design-system/SKILL.md` | 선택 | `:17–20, 24–42, 96–141`. 단계 라우터·재개·독립 리뷰 인계. **스킬 자체는 agent를 생성하지 않음**. |
| `design-system/references/1-parse-arguments.md` | 선택 | `:32–42, 44–81, 86–122`. 이름/retrofit, Summary와 minimal 5절. `:121–122`의 “retrofit regardless of tier”는 앞의 tier별 결손 판정과 충돌하므로 정리 필요. |
| `design-system/references/2-gather-context.md` | 선택 | `:23–58, 71–107, 146–240`. minimal은 brief에서 Category/Layer/Priority 추론. registry·의존 GDD·엔진 모듈 조건부. framework-map 우선 읽기 추가. |
| `design-system/references/3-file-skeleton.md` | 선택 | `:17–29, 31–107, 109–123`. 골격·Summary·active.md. full/standard 생략 규칙만 상세하여 minimal 5절+Summary를 명시적으로 동일 적용해야 함. |
| `design-system/references/4-section-design.md` | 선택 | `§Section Cycle, A–H, Optional Sections`; `:22–29, 85–98, 145–150, 375–409, 475–518`. 절별 질문·승인·즉시 저장, Given/When/Then. minimal 수치는 Core Rules에 둘 수 있고 시청각/feel은 선택. |
| `design-system/references/5-post-design.md` | 선택 | `:21–54, 77–132, 135–180, 195–208`. Summary·자체 확인·registry·인덱스 갱신·별도 리뷰 인계. 없는 registry/index는 통과 또는 오류로 오인하지 않음. |
| `design-system/references/lens-creative-director.md` | 확장 | `§Section B, CD-GDD-ALIGN`. full의 감정 목표·pillar 비교. 현재 solo에서는 해당 검토 생략, 역할 파일 대신 로컬 렌즈임. |
| `design-system/references/lens-systems-designer.md` | 확장 | `§D/E/G`. 수식 경계·예외·조정값 확인. 원형 solo에서는 specialist 단계 생략. 같은 체크를 선택적으로 쓰려면 위임했다고 보고하지 않음. |
| `design-system/references/lens-qa-lead.md` | 확장 | `§Checks`. AC의 규칙·수식·경계·통합 범위 검토. solo에서는 스킬 본체가 AC를 직접 작성하며 이 specialist 단계는 생략. |
| `design-system/references/lens-specialists.md` | 확장 | `§Roles, Visual/Audio`. Section C·시청각 분기의 관점 목록. full로 실행할 때 파일 필요, 현재 역할 전부 설치할 근거는 아님. |

렌즈를 최소 복사에서 제외하려면 solo 전용으로 라우터를 제한하고 남은 참조도 정리한다. 향후 `--review full`을 지원할 스킬로 가져오면 위 4개 렌즈도 함께 둔다. 팀 역할을 붙인 과거 설명보다 SKILL의 “no agents”가 현재 실행을 설명한다(`SKILL.md:17–20`과 `:135–136`의 표현 차이).

### 3.3 전체 구현 구조 — 10개

| 파일 | 판정 | 근거·필요성 |
|---|---|---|
| `create-architecture/SKILL.md` | 선택 | `:17–48, 88–125`. minimal 선택 기능. 원형 후속 안내의 test-setup/UX/게이트 전체 필수 문구는 현재 흐름에 적용하지 않도록 변경. |
| `create-architecture/references/0-load-context.md` | 선택 | `:18–83, 84–145, 193–210`. engine 문서, minimal brief 예외, TR 임시 ID, 기존 문서 선택 갱신. 기존 기획/로컬 엔진 근거로 대체 입력 명시 필요. |
| `create-architecture/references/1-layers.md` | 선택 | `:8–41`. 계층·경계·소유권. Platform은 프로젝트 코드 모듈이 아님. Unreal 프레임워크 클래스와 별도 축으로 표현. |
| `create-architecture/references/2-module-ownership.md` | 선택 | `:8–28`. Owns/Exposes/Consumes/Engine APIs. “module”을 UBT 모듈 증설 지시로 오인하지 않게 구분. |
| `create-architecture/references/3-data-flow.md` | 선택 | `:8–22`. 프레임·이벤트·초기화·저장 흐름. 2인 요청→서버 판정→복제→호스트/클라이언트 표시 흐름 추가, save/load는 요구 없음 표기. |
| `create-architecture/references/4-api-boundaries.md` | 선택 | `:8–23`. 전제·보장·API 경계. 언어는 없는 technical-preferences 대신 project.yaml의 C++; 네트워크 요청 권한·실패 결과까지 기록. |
| `create-architecture/references/5-adr-audit.md` | 선택 | `:8–48`. 설계와 TR baseline의 ADR 충족 확인. 모든 미충족 요구를 신규 ADR 의무로 만드는 문구는 선택 범위에 맞게 제한. |
| `create-architecture/references/6-missing-adrs.md` | 선택 | `:8–19`. 구현 전/해당 기능 전/연기 가능한 결정을 구분. minimal에서도 필요한 선택만 기록. |
| `create-architecture/references/7-write.md` | 선택 | `:13–64, 69–107`. 골격·부분 갱신·버전·서명. solo/lean은 TD/LP 생략, full만 역할과 gate 필요. |
| `create-architecture/references/8-handoff.md` | 선택 | `:8–46`. active.md와 결정 인계. 일반 tests/unit·CI·전체 UX 관문을 Unreal minimal의 필수 완료 기준으로 사용하지 않게 수정. |

### 3.4 개별 기술 결정 — 8개

| 파일 | 판정 | 근거·필요성 |
|---|---|---|
| `architecture-decision/SKILL.md` | 선택 | `:27–45, 47–87, 94–129`. terse는 구조표를 줄이지 않음. 선택 ADR도 Proposed와 채택을 구분. |
| `architecture-decision/CONTRACT.md` | 선택, 동시 갱신 | `§Inputs/Outputs/Hard Constraints/Downstream`; `:17–31, 38–62, 65–94`. writer와 소비자 계약. 경로·역할·수정 허용 범위를 구현 본문과 일치시켜야 함. |
| `architecture-decision/references/0-retrofit.md` | 선택 | `:15–24, 42–63`. Status/의존/엔진/기획 연결/날짜 누락 보강. 기존 절 보존. 새 ADR만 시작하면 당장 실행되지는 않지만 모드 유지 시 필요. |
| `architecture-decision/references/0-accept.md` | 선택, 필수 동반 | `:10–50, 53–84`. 유일한 Accepted 경로·선행 ADR 채택 검사·연결 스토리 Ready 전환. 문서 작성만 가져오고 이 파일을 빠뜨리면 소비자가 요구하는 채택 상태에 도달할 수 없음. |
| `architecture-decision/references/1-engine-context.md` | 선택 | `:8–46`. domain과 근거 파일. 프로젝트 미설정과 단지 engine-reference 폴더 부재를 구분하도록 변경. |
| `architecture-decision/references/3-gather-context.md` | 선택 | `:8–48, 50–78`. architecture registry 충돌 선검사. 신규 대상에 registry가 없을 때의 빈 초기 상태·기존 ADR 직접 검토 대안이 필요. |
| `architecture-decision/references/4-decide.md` | 선택 | `:8–53, 66–71`. 문제·대안·의존·기획 연결 추출 후 확인. UNKNOWN을 None으로 바꾸지 않음. 주요 선택을 개별 확인. |
| `architecture-decision/references/5-generate.md` | 선택 | `:19–150, 153–173, 175–220, 222–260`. 전체 ADR 필드·엔진 specialist·registry. **solo라도 엔진 specialist는 실행**, TD-ADR만 생략. GDD 동기화 수정과 CONTRACT의 불변 규칙 충돌도 해소 필요. |

### 3.5 구조 검토 — 11개

| 파일 | 판정 | 근거·필요성 |
|---|---|---|
| `architecture-review/SKILL.md` | 선택, minimal 지원 수정 | `:19–43, 45–79`. no agents. coverage/consistency/engine/single-gdd/rtm 범위 선택. 현재 원형은 minimal을 not applicable로 기술. |
| `architecture-review/references/1-load.md` | 선택 | `:19–82, 84–179`. review receipt·범위·Summary·선택 절 읽기. 기획/ADR 부재와 UNRESOLVED를 통과로 보지 않음. framework-map도 receipt 입력에 포함해야 함. |
| `architecture-review/references/2-extract-requirements.md` | 선택 | `:8–38, 40–67`. TR 기존 ID·임시 baseline 대조·의미 확인. 선택 기획 절을 요구사항 입력으로 받도록 확장. |
| `architecture-review/references/3-traceability.md` | 선택 | `:25–44, 63–127`. Accepted/Proposed/불명 구분과 RTM. test_root 하드코딩 및 “파일 존재=passing test” 요약 불일치 수정. |
| `architecture-review/references/4-conflicts.md` | 선택 | `:8–34, 36–89`. 소유권·계약·예산·의존 충돌. adr-dep-graph.sh 필요, 누락 의존은 clean graph가 아님. |
| `architecture-review/references/5-engine.md` | 선택 | `:16–60, 64–107`. pinned 버전 대조·렌즈·기획 피드백. 없는 systems-index 갱신을 강요하지 않게 guard 필요. |
| `architecture-review/references/lens-engine.md` | 선택 | `:18–23, 54–64`. 현재 Unreal 검토에서 실제 읽는 렌즈. GAS 등은 프로젝트가 채택했을 때만 해당, 전체 프레임워크 도입 명령으로 해석하지 않음. |
| `architecture-review/references/6-document-coverage.md` | 선택 | `:8–20`. master 문서 없는 full scope는 NOT ASSESSED. 선택 ADR의 consistency/engine 리뷰에서는 이 차원을 범위 밖으로 명시해 불필요한 master 문서 작성을 막음. |
| `architecture-review/references/7-report.md` | 선택 | `:9–83`. Mode/Workflow/엔진/분모·판정. 부분 검토·미확인과 PASS 구분 유지. |
| `architecture-review/references/8-write.md` | 선택 | `:16–51, 117–145, 148–205`. 보고서/RTM/TR registry 선택 저장·부분 갱신·receipt·상태 기록. 현재 최소에는 TR 산출물 필수 아님. |
| `architecture-review/references/9-handoff.md` | 선택 | `:12–32`. 원형 테스트 루트/CI/접근성/pattern 필수 목록을 현재 minimal 선택 리뷰의 관문으로 만들지 않게 수정. |

### 3.6 규칙 집약·단일/교차 기획 검토 — 10개

| 파일 | 판정 | 근거·필요성 |
|---|---|---|
| `create-control-manifest/SKILL.md` | 현재 제외, 누적 후 선택 | `:25–48, 74–104, 184–200, 293–303`. Accepted ADR 집합 A와 누락 집합 공개. 독립 템플릿 파일은 없고 본문에 양식 내장. |
| `design-review/SKILL.md` | 선택 | `:31–46, 50–147, 169–208, 212–285, 289–470`. 구조·실현성·registry·review log. solo는 Phase 1–4만 실행하므로 원형에서는 Phase 5의 로그·인덱스 저장이 실행되지 않음. |
| `review-all-gdds/SKILL.md` | 현재 제외, 복수 GDD 후 선택 | `:47–80, 108–141`. minimal 불가 서술과 permissive 공통 정책 차이, 독립 2개 game-designer 위임. `review_mode`를 resolve하지 않으므로 solo라고 위임이 사라지지 않음. |
| `review-all-gdds/references/1-load.md` | 현재 제외, 복수 GDD 후 선택 | `:16–54, 58–117`. Summary·registry·변경집합·2개 이상 GDD. optional index 부재 분기 보강 필요. |
| `review-all-gdds/references/2-consistency.md` | 현재 제외, 복수 GDD 후 선택 | `§2a–2f`, `:58–91`. 양방향 의존·규칙·참조·소유·수식·AC 모순. Summary 스캔만으로 Cross-References 표를 빠뜨리지 않도록 load 계약과 맞춤. |
| `review-all-gdds/references/3-design-holism.md` | 현재 제외, 복수 GDD 후 선택 | `§3a–3g`. 주의 분배·지배 전략·반복 루프·pillar. 이 게임은 장기 성장/화폐가 확정되지 않았으므로 해당 없음과 미평가를 구분; 근거 없는 3–4개 인지 한계를 확정 법칙으로 쓰지 않음. |
| `review-all-gdds/references/4-scenarios.md` | 현재 제외, 이후 특히 유용 | `:12–51, 72–85`. 여러 시스템 경계의 경쟁·상태·피드백 검토. 동시 획득, 배치와 기차 진입, 호스트 종료 시나리오로 교체. |
| `review-all-gdds/references/5-report.md` | 현재 제외, 복수 GDD 후 선택 | `:14–18, 77–118`. N/M·Not read·Focus·판정. 읽을 수 없는 문서/빈 문서는 PASS가 아님. |
| `review-all-gdds/references/6-write.md` | 현재 제외, 복수 GDD 후 선택 | `:8–46`. ISO 날짜 보고서·정확한 Needs Revision·active.md. 보고서 경로 변경 시 review-scope도 함께 수정. |
| `review-all-gdds/references/7-handoff.md` | 현재 제외, 복수 GDD 후 선택 | `:10–30`. 선택 focus를 전체 통과로 취급하지 않음. minimal에서 gate/전체 architecture로 자동 확장하지 않게 변경. |

### 3.7 변경 영향·역문서화 — 3개

| 파일 | 판정 | 근거·필요성 |
|---|---|---|
| `propagate-design-change/SKILL.md` | 현재 입력 없음, 조건부 | `:31–35, 44–66, 84–124, 126–159, 208–263`. diff→연결 ADR 분류→개별 대응. minimal 중단 수정 없이는 선택 ADR를 놓침. 원형 직접 ADR 편집은 Superseded Status뿐. |
| `reverse-document/SKILL.md` | 현재 제외, 구현 누적 후 선택 | `:37–65, 122–149, 198–253, 284–300`. 실구현 충분성·출처·미확인 의도 보존. minimal은 GDD 대신 brief 생성; 기존 기획 덮어쓰기 금지 분기 추가. `architecture` 출력은 정규 ADR 계약과 다름. |
| `reverse-document/references/from-a-game.md` | Unreal 이관 제외 | `:6–10, 17–24, 38–54, 93–108`. HTML/JS·스크린샷·브라우저 brief·door.md 전용. Unreal 코드 역문서화에는 쓰지 않음. |

### 3.8 UX — 9개

| 파일 | 판정 | 근거·필요성 |
|---|---|---|
| `ux-design/SKILL.md` | 선택 | `:23–40, 51–67, 90–107, 131–149`. screen/hud/patterns/accessibility 모드. minimal에서는 필요할 때만. “primary agent set in frontmatter”라는 `:133` 서술은 실제 frontmatter에 agent 지정이 없어 정정 필요. |
| `ux-design/references/2-gather-context.md` | 선택 | `:27–52, 54–78, 98–128, 147–189`. 기존 UX retrofit·입력 방식·기획 UI 요구. player journey는 읽기만 하며 생성 스킬은 없음. Doc/기획.md의 조작/경고를 직접 입력받게 변경. |
| `ux-design/references/3-file-skeleton.md` | 선택 | `§네 모드 골격`, `:29–34, 148–152, 284–379`. Template 식별자·플랫폼·접근성 tier. active 모드만 생성. 기존 파일은 새 골격으로 덮지 않음. |
| `ux-design/references/4-authoring.md` | 선택 | `:8–31`. 절별 초안 승인 후 또 쓰기 확인을 반복. 이미 승인한 같은 절·경로는 한 질문으로 합치는 변경 권장. |
| `ux-design/references/sections-ux-spec.md` | 선택, 화면 모드 | `§A–I`, 특히 `:145–175, 196–227, 262–286`. 입력·이벤트·데이터 소유자·AC. 로비/초대/종료 흐름 등 필요한 화면만. |
| `ux-design/references/sections-hud.md` | 선택, HUD 모드 | `:33–88, 99–154`. 정보 우선순위·피드백·표시 상태·예산. combat/dialogue/exploration을 강제하므로 실제 라운드 구간으로 교체. |
| `ux-design/references/sections-patterns.md` | 확장 | `:7–63`. 기존 명세에서 공통 패턴을 추출·추가. 소수 HUD/메뉴의 최소 묶음에는 별도 라이브러리 불필요. |
| `ux-design/references/5-crossref-handoff.md` | 선택 | `:8–35, 43–75`. 기획·이동·빈 데이터·접근성 대조 후 ux-review. 접근성 모드 경로를 일반 UX 경로로 잘못 기록하지 않도록 보완. |
| `ux-review/SKILL.md` | 선택 | `:47–91, 95–234, 267–291, 315–333`. Template로 체크리스트 선택, 읽기 전용·자문. 접근성 미정은 NOT ASSESSED, minimal pattern 부재는 N/A. HUD 맥락과 과도한 전체 패턴 필수 목록 조정. |

위 파일 수는 `4 + 10 + 10 + 8 + 11 + 10 + 3 + 9 = 65`다. 모든 모드를 그대로 지원하려면 소유 폴더 전체를 이관한다. 모드별 선택 복사는 남은 런타임 참조를 끊는 수정과 한 묶음이다.

## 4. 실제 동반 의존성과 조건

### 4.1 스크립트·실행 기반

| 원본 실제 경로 | 소비자·조건 | 이관 판단·변경 지점 |
|---|---|---|
| `S/.claude/hooks/yaml-helper.sh` | 14개 스킬의 resolve_config. reverse-document는 본문 Phase 1에 부트스트랩이 있음 | 채택한 스킬의 원형 설정 해석을 유지하면 필요. hooks 폴더의 **라이브러리**이며 이벤트 등록 필수 아님. Bash와 Python (`python → python3 → py`, PyYAML 불필요), `:66–68` |
| `S/.claude/engines/engines.tsv`, `S/.claude/scripts/engine-row.awk` | helper의 엔진 기본값/engine_row 기능 | helper가 `:1426–1440`에서 참조. automation/workflow만 조회하는 M0/M1의 직접 필수는 아니나 engine_*까지 재사용하면 동반. 대상 project.yaml의 루트/명령을 우선하고 웹 starter는 가져오지 않음 |
| `S/.claude/scripts/registry.sh` | design-system slice/validate, create-architecture next-id tr, architecture-decision validate, architecture-review validate | 상세 묶음 선택 시. `:142–146`의 세 registry 경로, `:273–278`의 ADR/스토리 경로, `:315–319`의 production/epics 검색을 함께 바꿈. 독립 Bash/awk 스크립트이며 yaml-get.awk를 source하는 의존은 없음 |
| `S/.claude/scripts/adr-status.sh` | manifest·propagate 직접, 후속 create-stories/dev-story/story-readiness | 선택 ADR를 사용하면 중요한 공통 reader. `:87–93`은 docs/architecture 및 lowercase adr-*.md 고정. **외부 `.claude/docs/templates/architecture-decision-record.md`도 읽어 누락 절을 계산**(`:88, 134–136`)하므로 내장 양식만 고치면 안 됨 |
| `S/.claude/scripts/adr-dep-graph.sh` | architecture-review 의존 순환 검사 | 해당 리뷰 채택 시 필요. 기본 docs/architecture 대신 명시 폴더 인자를 쓸 수 있음(`§Usage, :17–18, 66`). 부재/UNKNOWN 의존을 clean으로 읽지 않음 |
| `S/.claude/scripts/md-section.sh` | manifest 절 추출, propagate 절 추출/Status replace, 후속 stories | 해당 기능 채택 시. 경로 인자 기반이라 폴더 자체를 하드코딩한 소비자 호출을 수정. generic extract/list/replace 기능이며 replace는 실제 문서를 바꿈 |
| `S/.claude/scripts/gdd-structure-check.sh` | design-review의 절 존재 판정 | 리뷰 채택 시. `:49–58`의 영문 절 이름·Detailed Rules/Design 별칭 유지. 명시 파일 검사는 가능, 기본 전체 검색은 design/gdd 고정(`:129–140`). 기존 한국어 개요를 이 검사에 직접 넣어 GDD 결손으로 판단하지 않음 |
| `S/.claude/scripts/review-receipts.sh` | design-review·architecture-review의 재검토 freshness | 리뷰 채택 시. Git hash-object 또는 sha1sum/shasum과 일반 POSIX 도구 필요(`:101–136`). 경로 인자형. 새 framework-map·기획·관련 spec을 **check와 hash 양쪽**에 동일 포함 |
| `S/.claude/scripts/review-scope.sh` | review-all-gdds의 since-last-review만 | 전체 교차 리뷰만 쓰면 미호출. 변경분 모드를 유지하면 필요. `:38, 52, 68`의 GDD 경로와 ISO 날짜 보고서 기준을 함께 변경 |
| `S/.claude/scripts/game-read.sh` | reverse-document brief 및 HTML 도입 경로만 | Unreal 범위에서 제외. 일반 Unreal design/architecture 역문서화의 의존이 아님 |

위 스크립트가 사용하는 Bash/awk/grep/sed/find/sort/Git 환경은 추후 이관 시 확인할 항목이다. 이번에는 실행·설치·테스트하지 않았다. `yaml-get.awk`는 여러 다른 CCGS helper의 의존이지만 이 표의 planning helper 때문에 무조건 가져와야 한다는 근거는 찾지 못했다. `story-facts.sh`, `story-status.sh` 등은 **구현 흐름 소비자 측** 의존성으로 그 이관에서 결합한다.

### 4.2 문서·템플릿·registry

아래 경로는 모두 원본 실재 파일이다. 폴더 통복사 목록이 아니라 채택 기능별 의존 목록이다.

| 경로 (`S/` 기준) | 필요 조건·판단 |
|---|---|
| `.claude/docs/config-resolution.md`, `.claude/docs/automation-modes.md`, `.claude/docs/workflow-modes.md` | 설정 계약을 유지하는 모든 묶음. 기존 공통 지침을 다시 쓰는 작업과 구분한다. 효과 명세 전체 effects-map은 평상시 런타임 필수 아님(`workflow-modes.md:12–19`). |
| `.claude/docs/templates/game-brief.md` | M0. 여섯 필수 필드·영문 제목·Features `[ ]/[x]` 계약(`:8–19, 57–86`). 내용은 한국어 가능. `In your words`에 기존 기획을 복제하기보다 원문 링크·발췌 출처와 원문 보존 정책으로 수정. |
| `.claude/docs/templates/game-concept.md` | brainstorm full-concept 분기를 유지할 때만. 기존 기획의 대체 원본으로 생성하지 않음. |
| `.claude/docs/templates/systems-index.md` | map-systems. 고정 7개 열과 Category 9종, 의존/우선순위/상태 계약(`:20–43, 94–96`). “custom categories 가능”(`:45–46`)은 writer의 9종 분기와 충돌할 수 있어 임의 확장 금지 또는 모든 소비자 갱신. |
| `.claude/docs/templates/game-design-document.md`, `.claude/docs/templates/guidance/game-design-document-guide.md` | 선택 GDD. 골격/가이드/스킬의 Summary, 절명, 표 열을 동시 변경. guide는 해당 절만 읽기. |
| `.claude/docs/templates/quick-design-tuning.md`, `quick-design-change.md`, `quick-design-new-system.md` | 실재하지만 quick-design은 **본문 내 양식으로 작성**, 직접 읽으라는 의존은 없음. 중앙 양식으로 통일할 때만 채택하고 본문 중복 제거. JSON 경로도 양쪽 동시 수정. |
| `.claude/docs/templates/architecture-decision-record.md` | ADR writer 본문과 동기화, 특히 adr-status의 누락 절 기준. `Status`, `Date`, `Last Verified`, `Summary`, Engine Compatibility/Layer, ADR Dependencies 등을 유지. |
| `design/registry/entities.yaml` | 여러 GDD가 공유할 실제 사실이 생길 때만. `source`, `referenced_by` block list, active/deprecated와 값·단위. 주석 예제는 실제 엔트리가 아님(`:45–54, 64–84`). 최소 스킬 설치의 선행 파일로 강제하지 않음. |
| `docs/registry/architecture.yaml` | ADR 원형 registry 검사를 유지하면 필요. **빈 구조로 초기화**하거나 부재 시 실제 ADR 검토 대안을 구현. 원본 주석 예제·타 게임 수치를 채택 결정으로 복사하지 않음. Proposed ADR에서 등록한 stance를 Accepted 제약으로 오인하지 않도록 상태 필터 추가. |
| `docs/architecture/tr-registry.yaml` | 상세 TR 연결을 채택할 때만. 실재하는 빈 registry(`requirements: []`). ID 영구성·상태·writer/reader 계약은 `:3–38`. TrackBuilding의 REQ 체계로 자동 교체하지 않음. |
| `.claude/docs/templates/architecture-traceability.md` | 추적성 문서의 별도 양식 후보. 현재 architecture-review는 `references/8-write.md`에 자체 양식이 있어 이 파일을 직접 읽는 필수 의존은 아님. 중앙화할 때만 동기화. |
| `.claude/docs/templates/ux-spec.md`, `hud-design.md`, `interaction-pattern-library.md`, `accessibility-requirements.md` | UX 활성 모드별. skeleton은 자체 포함이나 ux-spec 입력 체크와 HUD AC 예제는 실제 양식 절을 참조. accessibility skeleton은 외부 양식 절 목록을 따른다. |
| `.claude/docs/templates/guidance/ux-spec-guide.md`, `hud-design-guide.md`, `accessibility-requirements-guide.md` | UX 해당 모드의 깊이 보완, 접근성은 활성 절의 가이드가 직접 입력. 전체 가이드를 매번 로드할 필요 없음. |
| `.claude/docs/templates/guidance/interaction-pattern-library-guide.md` 및 `interaction-pattern-library-guide-standard-controls.md`, `interaction-pattern-library-guide-navigation-feedback.md`, `interaction-pattern-library-guide-game-specific.md` | patterns 모드 가이드가 다시 3개 주제로 연결. 패턴 분기를 유지하면 필요한 topic 파일까지 동반. |
| `.claude/docs/templates/player-journey.md` | 선택 수작업 작성용. **player-journey 생성 전용 스킬은 없음**(`ux-design/references/2-gather-context.md:41–52`). 없다고 UX를 차단하지 않음. |
| `.claude/docs/templates/design-doc-from-implementation.md`, `architecture-doc-from-code.md`, `concept-doc-from-prototype.md` | reverse-document 선택 시 타입별. SKILL의 `templates/…` 표기는 실제 경로 `.claude/docs/templates/…`로 명확히 고침. architecture-doc-from-code는 정규 ADR와 호환되지 않으므로 그대로 governing ADR로 내보내지 않음. |
| `.claude/docs/templates/session-state.md`, `.claude/docs/context-management.md` | active.md 재개 형식을 유지할 때. 스킬의 수동 상태 갱신과 세션 hooks 자동 주입은 별개. 이 절차만으로 session-start/stop 전체 훅 설치를 요구하지 않음. |
| `.claude/docs/directory-structure.md`, `.claude/docs/code-root-resolution.md`, `.claude/docs/error-recovery-protocol.md` | 각각 RTM 테스트 루트 안내, reverse-document 코드 루트, architecture/cross-GDD 오류 복구의 명시 참조. 필요한 절만 대상 경로와 일치시킴. |
| `.claude/docs/workflow-catalog.yaml` | UX 접근성 위치와 전체 다음 단계·산출물 검사의 소비자. 이 문서만을 위해 통째로 가져오지 않음. catalog를 채택할 때 새 brief/GDD/ADR/UX 경로를 같은 변경에 반영. |

기존 `T/CLAUDE.md`, `T/project.yaml`, `T/Doc/기획.md`, `T/.claude/docs/unreal-framework-map.md`는 **복사 대상이 아니라 유지할 입력**이다. 없는 technical-preferences를 원본 기본값으로 채워서 기존 기술 정책처럼 만들지 않는다. 원본 `.claude/settings.json`, `project.yaml`, rules/agents 전체 통복사도 추천하지 않는다.

### 4.3 역할·gate·엔진 문서의 동적 의존

- **M0/M1:** 역할 파일·director gate가 필요 없다. brainstorm의 full 분기 제거가 전제다.
- **map-systems의 full review:** `.claude/agents/technical-director.md`+`director-gates/td-system-boundary.md`, `producer.md`+`pr-scope.md`, `creative-director.md`+`cd-systems.md`. solo/lean은 모두 생략(`SKILL.md:198–207, 258–273, 318–335`).
- **brainstorm의 full concept+full review:** creative-director/cd-pillars, art-director/ad-concept-visual, technical-director/td-feasibility, producer/pr-scope. `.claude/docs/director-gates.md`와 개별 gate 파일 필요. 현재 minimal은 full-concept 자체를 읽지 않는다.
- **design-system:** 네 lens 파일과 full CD-GDD-ALIGN의 `.claude/docs/director-gates/cd-gdd-align.md`; **agent 역할 설치가 직접 의존이 아님**. architecture-review도 `references/lens-engine.md`를 직접 적용하며 agent 없음.
- **create-architecture full review:** technical-director/td-architecture와 lead-programmer/lp-feasibility. solo/lean은 생략.
- **architecture-decision:** `engine.name=Unreal`이면 `.claude/agents/unreal-specialist.md`를 **solo에서도** 호출한다(`references/5-generate.md:153–165`). full은 technical-director/td-adr 추가. `ue-replication-specialist.md`, `ue-blueprint-specialist.md`, `ue-umg-specialist.md`는 이 스킬이 자동 호출하는 필수 역할이 아니고 필요한 도메인 검토를 명시 연결할 때만 추가한다.
- **design-review full:** 도메인별 `.claude/agents/game-designer.md`, `systems-designer.md`, `qa-lead.md`, `network-programmer.md`, `gameplay-programmer.md`, `level-designer.md`, `ux-designer.md`, `ui-programmer.md`, `audio-director.md`, `performance-analyst.md`, `unreal-specialist.md` 등 중 실제 도메인과 creative-director 종합. economy/narrative 역할은 실제 요구가 있을 때만. `SKILL.md:225–285`가 동적 선택표다. solo/lean은 이 분기 없음.
- **review-all-gdds:** `.claude/agents/game-designer.md` 2개 작업은 review 설정과 독립인 원형의 명시 병렬 단계다. 유지하면 이 역할이 필요하고, 단일 세션 버전으로 줄이려면 SKILL의 위임 계약·부분 실패·보고서 표시를 함께 수정한다. 이번 분석에서는 위임하지 않았다.
- **manifest/propagate full review:** technical-director와 각각 `td-manifest.md`/`td-change-impact.md`. solo/lean은 skip. 세 gate의 결론만으로 ADR를 Accepted로 바꾸지는 않는다.
- **UX:** 필요 주제에 ux-designer, ui-programmer, art-director, game-designer, accessibility-specialist를 연결하는 선택 기능이다. `.claude/skills/team-ui/SKILL.md:41–45`는 individual에서도 ui-programmer+ux-designer 두 역할을 사용하므로 “solo=agent 0개”라는 일반화는 틀리다. team-ui 전체는 지금 이관 필수가 아니다.

엔진 참조의 실제 파일은 `S/docs/engine-reference/unreal/{VERSION.md,breaking-changes.md,deprecated-apis.md,current-best-practices.md}` 및 `modules/{networking,input,physics,navigation,ui,animation,audio,rendering}.md`다. `Core`/`Scripting`은 스킬의 도메인 어휘지만 해당 이름의 module 파일은 원본 Unreal 폴더에 없다. 없음→검토 생략을 성공으로 보고하면 안 된다. `PLUGINS.md`와 `plugins/common-ui.md`, `gameplay-ability-system.md`, `gameplay-camera-system.md`, `pcg.md`는 **그 플러그인을 설계가 실제 사용하거나 역할이 참조할 때만** 동반한다.

원본 VERSION은 5.8/최신 hotfix 5.8.3·설치 버전 NOT DETERMINED로 적혀 있다(`:5–9`). 대상은 5.8.4이고 프레임워크 맵에 설치 소스 근거가 있다. 원본의 Iris 일반 설명(`modules/networking.md:6–18`) 등을 대상의 복제 기본값 결정으로 옮기지 않는다. 현 설치의 legacy 기본·Iris 조건부 설명은 `T/unreal-framework-map.md:53`을 우선하고 필요한 사실만 재확인한다. 이 보고서는 원본 엔진 참조의 외부 API 주장 전체를 검증한 문서가 아니다.

## 5. 문서·질문 흐름·후속 구현 계약

### 5.1 기존 기획을 유지하는 경로안

아래는 **이관 시 채택할 경로 추천안**이며 아직 생성·확정하지 않았다. `Doc/기획.md`를 CCGS 양식으로 재작성하지 않는다. 영문 machine-readable 제목을 유지한 작은 파생 문서를 두어 한국어 기존 기획과 스크립트 계약을 함께 지킨다.

| 원본 기본 경로 | 대상 경로 추천안 (기존 표시 외 모두 예정) | 책임 |
|---|---|---|
| 원본에 대응 없음 | `T/Doc/기획.md` **기존 유지** | 사용자의 전체 게임 방향·규칙 원본 |
| `design/game-brief.md` | `T/Doc/game-brief.md` | 현재 구현 범위·Build order만 추출. `Source: Doc/기획.md §…`와 원문 변경 기준 기록 |
| `design/quick-specs/` | `T/Doc/QuickSpecs/` | 작은 변경의 전후·AC·영향. 기획 변경을 적용했는지/pending인지 분리 |
| `design/gdd/*.md`, `systems-index.md` | `T/Doc/GDD/…` | 선택 시스템 상세 기획·인덱스. 원본처럼 lowercase kebab-case 파일명 추천 |
| `docs/architecture/…` | `T/Doc/Architecture/…` | architecture.md, `adr-NNNN-*.md`, 선택 TR registry/RTM/manifest/impact report |
| `design/registry/entities.yaml`, `docs/registry/architecture.yaml` | `T/Doc/Registry/entities.yaml`, `architecture.yaml` | 채택 시 공통 사실·기술 제약 색인. 원본 값 복제가 아닌 대상의 빈 초기 구조로 시작 |
| `design/ux/…`, `design/accessibility-requirements.md`, `design/player-journey.md` | `T/Doc/UX/…`, `T/Doc/accessibility-requirements.md`, `T/Doc/player-journey.md` | 선택 UX·접근성·journey. accessibility가 UX 폴더 밖이라는 계약을 유지해 위치를 하나로 정함 |
| `production/epics/`, `production/session-state/active.md` | **작업 흐름 담당과 함께 확정 필요** | 여기서 독자적으로 새 루트를 결정하지 않는다. 원본 reader가 이 위치를 고정 사용함을 전달하고 모든 writer/reader를 동시에 변경 |

단순 prefix 바꾸기만으로 완료되지 않는다. glob·상태 helper·template·CONTRACT·registry의 `source/gdd/adr/referenced_by`·review receipt·catalog가 모두 같은 경로를 써야 한다. 대소문자도 기록상 통일한다. 현재 프레임워크 맵의 위치는 그대로 유지하고 참조를 추가한다.

brief의 여섯 필수 필드는 `Pitch`, `Core loop`, `Goal & fail state`, `Features`, `Out of scope`, `Build order`다(`game-brief.md:8–19`). 기존 기획은 목표·실패·조작을 제공하지만 구현할 Features의 coarse 분할, 순서, 당장 제외할 범위는 확정돼 있지 않다. 이 세 항목은 추출 초안으로 제시하고 사용자의 기존 지시에서 답이 없는 선택만 확인한다. `Features [x]`는 실제 구현 근거가 있을 때만 표시하며 현재 전부 구현 완료로 표시할 수 없다. `Out of scope`는 영구 삭제 목록이 아니라 이번 구현 묶음에서 뒤로 둔 항목이다. 기존 2인·Steam 목적을 임의로 “멀티플레이 제외”로 축소하지 않는다.

### 5.2 GDD·TR·ADR·manifest의 계약

| 산출물 | 보존할 필드·의미 | 후속 소비자 |
|---|---|---|
| 선택 GDD | `## Summary`+Quick reference는 별도 필수. minimal 자발 작성은 Overview/Detailed Design(=Detailed Rules)/Edge Cases/Dependencies/Acceptance Criteria 5절. 수치는 Core Rules에 명시해도 됨. full 8절·standard 조건부 Formulas를 minimal에 강제하지 않음 | design-review, create-architecture, architecture-review, review-all-gdds, create-epics. 단순 summary grep으로 누락 문서를 제외하면 안 됨 |
| 시스템 인덱스 | `# / System Name / Category / Priority / Status / Design Doc / Depends On`; 9개 Category, 설계 상태의 정확한 문자열. `Designed`와 `Approved`, `Needs Revision`을 구분 | design-system의 domain/role 선택, 후속 epics·next system. 선택 GDD만 있을 때 인덱스 생성 필수 아님 |
| TR | `TR-[system]-NNN`, system·gdd·requirement·created/revised·status. 영구 ID 재사용/삭제 금지. create-architecture baseline의 provisional을 architecture-review가 registry로 확정 | create-stories/dev-story/story-readiness/story-done. **현재 minimal 스토리는 TR 대신 Brief feature N**. 중앙 TR 도입은 선택이며 TrackBuilding의 REQ를 확정 체계로 가져오지 않음 |
| ADR | `adr-NNNN-slug.md`; `## Status/Date/Last Verified/Summary/Engine Compatibility/ADR Dependencies/Context/Decision/Alternatives Considered/Consequences/GDD Requirements Addressed/Performance Implications/Validation Criteria` 등 본문·양식 계약. `Decision` 아래 Implementation Guidelines, Engine Compatibility의 **Domain과 Layer는 별도** | adr-status의 status/layer/version, dep graph, manifest·epics/stories·dev-story. Proposed는 새 문서, Accepted는 별도 채택. None은 확인된 무의존이고 UNKNOWN은 미확인 |
| manifest | Engine, Last Updated, **Manifest Version**, ADRs Covered, Active; Foundation/Core/Feature/Presentation/Global별 required/forbidden/guardrails. 모든 규칙에 근거 | create-stories의 복사 규칙·버전, story-readiness/dev-story의 freshness. 최소 필수 아님. ADR의 대안을 거절했다는 사실을 맥락 없는 전역 금지로 넓히지 않도록 범위 명시 |
| UX | `Template` 식별자(UX Spec/HUD Design/Interaction Pattern Library/Accessibility Requirements), 플랫폼/입력·상태·소유 데이터·행동 피드백·AC, 접근성 tier의 확정/가정 | ux-review는 Template로 체크리스트 선택. UI 스토리는 관련 UX 파일을 직접 참조. 표시 데이터가 authoritative game state를 소유하지 않도록 framework-map과 연결 |

정규 ADR의 **Status가 Accepted라는 것과 구현 검증 성공은 별개**다. Last Verified는 문서 확인 날짜이고 빌드/2인 성공 증거가 아니다. 테스트 파일이 존재한다는 사실도 테스트 통과를 뜻하지 않는다.

### 5.3 후속 스킬과 맞춰야 할 핵심 공백

1. `create-stories/SKILL.md:91–145, 297–310`은 minimal에서 brief만 읽고, ADR/manifest를 `N/A`로 채운다. 따라서 선택 GDD·ADR·quick spec을 만들기만 해서는 구현에 전달되지 않는다. **전역 workflow는 유지한 채**, 스토리가 명시한 추가 설계 파일과 선택 governing ADR를 읽어 AC/Decision Summary/Implementation Notes/ADR Version에 옮기는 분기를 구현 담당과 같이 추가해야 한다. 선택 문서가 없으면 원래 brief-only 경로로 간다.
2. `create-stories`에는 `Your systems`의 `deepen — <system>` 연결도 있다(`:122–138`). 이것을 쓰면 `design-system`의 결과 경로와 `dev-story`의 deepen 인식이 같아야 한다. 다만 현재 게임을 임의의 thin stand-in/later 체계로 분류하는 결정은 이 보고서에서 하지 않는다.
3. `quick-design:285–286`의 “story의 GDD Reference에 spec 참조”와 원본 minimal stories의 GDD=brief 고정이 충돌한다. brief를 전체 방향 원본으로 남기고 **스토리의 직접 설계 참조에 QuickSpec 경로+절을 추가**하는 추천이다. 원본과 다른 필드를 새로 만들면 story template와 story-facts·readiness·dev-story까지 읽도록 동시에 바꿔야 한다.
4. `dev-story/SKILL.md:69–99`는 minimal도 **스토리가 명시한 ADR**는 검사하고 Proposed/Deprecated/Superseded/없음을 막는다. 그러나 정상 경로는 ADR 원문 대신 스토리에 복사된 결정·버전을 신뢰한다(`:85–96`). ADR 채택/수정 후 스토리의 요약·버전이 최신인지 확인하는 책임을 인계해야 한다. 과거 CONTRACT의 “원문을 읽는다” 설명만 믿지 않는다.
5. `architecture-decision accept`는 `production/epics/**/story-*.md`의 ADR ID+Blocked 조합으로 Ready로 옮긴다(`0-accept.md:53–84`). 다른 이유의 blocker·다른 Proposed ADR가 남은 경우까지 일괄 Ready로 바뀌지 않도록 잔여 조건 재검사를 추가한다. 새 작업 경로를 결정하면 이 검색도 함께 고친다.
6. `architecture-review`의 RTM은 현재 `Source/*/Private/Tests/**/*.cpp`를 검색한다(`3-traceability.md:92–96`), 후속 안내도 같은 기본 루트다. 이를 `project.yaml engine.test_root=Source/TrackBuilding/Tests`로 바꾸고 폴더 부재는 **예정/미구축**으로 표시한다. RTM의 COVERED(파일 있음)와 요약의 passing test(`:106–123`)를 **파일 연결 상태와 실행 결과 별도 열**로 분리하는 변경이 필요하다.
7. `design-review` solo는 출력만 하고 tracking write를 건너뛴다(`:44–46`). 새 세션에서 solo 리뷰를 실행했다고 해도 저장된 Approved receipt가 자동으로 생기지 않는다. 로그가 필요한 운영이라면 별도 저장 옵션을 만들고 “독립 검토를 수행했다”와 “기록을 저장했다”를 구분한다.

### 5.4 질문 흐름 추천

- 기존 기획·설정·지도에서 답을 먼저 읽는다. 장르·엔진·플랫폼·2인 여부·네트워크 모델을 반복해서 묻지 않는다. brainstorm의 “항상 콘셉트 선택”(`:48–51`)은 **신규/전환 모드에만** 남긴다.
- collaborative는 유지한다. 선택이 필요한 질문은 근거와 2–3개 대안을 보여 준 다음 답을 기다린다. 숫자·범위·모듈 소유자·RPC 권한을 모델이 임의 확정하지 않는다. 이미 승인한 범위의 읽기·분석·저장을 다시 묻는 절차는 제거한다.
- brief/quick spec은 구체적인 전체 초안과 경로를 먼저 제시하고 동일 내용의 “초안 승인→쓰기 승인”을 합친다. GDD/UX는 중단 복구가 필요하므로 절별 저장은 유지하되 같은 절에 승인 질문을 두 번 내지 않는다(`quick-design:244–255`, `ux-design/4-authoring.md:23–29`).
- GDD 작성 후 design-review, ADR 작성 후 architecture-review는 독립된 검토 문맥을 사용한다. 원형의 fresh session 권고를 보존한다. 이 보고서 작성 중에 별도 채팅이나 역할을 실행한다는 뜻은 아니다.
- 엔진 사실·접근성 기준·독립 검토·실행 증거가 없으면 각각 NOT ASSESSED/미확정/미실행으로 표시한다. missing source를 새 문서·새 기능의 자동 요구로 바꾸지 않는다.

## 6. Unreal용 변경안과 소비자 영향

아래는 **원본 위치 → 현재 문제 → 이관 시 변경안 → 영향**이다. 이번에 적용한 변경이 아니다.

| 원본 위치·근거 | 현재 문제 | 변경안 | 함께 맞출 소비자 |
|---|---|---|---|
| brainstorm `:45–51, 88–108`; map-systems `:81–91`; design-system `2-gather-context:23–58`; UX `2-gather-context:27–30` | 기존 기획을 찾지 못하고 새 아이디어·새 brief부터 시작 | `Doc/기획.md`를 우선 입력. brief는 추출/재개 모드, 원문과 다른 추정·미정만 표시. 기존 기획 본문 보존 | create-stories의 brief 입력·없는 brief 안내, next-step/catalog/artifact 검사 |
| 위 writer 및 각 references의 `design/…`, `docs/architecture/…` | 대상 `Doc/` 정책과 불일치 | §5.1의 경로안을 한 번 확정하고 writer/reader·helper·template·contract를 함께 수정 | registry.sh, adr-status/graph, gdd-structure, review-scope, receipts, 후속 스토리/완료/게이트 |
| quick-design `:92–94, 133, 223`; 동명 외부 3양식 | `assets/data/*.json` 강제는 Unreal 데이터 경로와 다름 | 실제 기능의 UDataAsset/UDataTable 또는 Config 수치 필드 위치를 기록. C++ 값·Blueprint 디폴트·에셋 중 무엇이 조정 원본인지 확인 후 한 곳 선택; 임의 .uasset 생성 지시 금지 | 스토리 Files/AC, 값 검토·밸런스·실행 확인. 에셋 경로와 “내부 필드명”까지 근거로 남김 |
| create-architecture `1-layers`, `2-module-ownership`, `3-data-flow`, `4-api-boundaries`; ADR `5-generate` | 일반 계층/신호 예제가 Unreal 객체 수명·머신 범위를 다루지 않음 | framework-map을 필수 입력. 상태마다 소유 클래스·서버/소유자/전체/로컬 가시성·변경 권한·RPC 진입 경로·복제 결과·수명·데이터 원본 표 추가 | GDD는 행동 계약, ADR/구현 설계는 클래스/API 배치. TrackBuilding C++ 모듈을 함부로 분할하지 않음 |
| create-architecture `0-load-context:18–50`; ADR `1-engine-context`; design-system `2-gather-context:175–228`; architecture-review `1-load,5-engine,lens-engine` | engine-reference 부재를 엔진 미설정과 혼동. 원본 VERSION의 5.8.3/설치 미확정이 대상 5.8.4와 다름 | project.yaml→기존 지도→필요한 로컬 엔진 소스 증거 순서로 읽기. 일반 참조는 보조·검증 대기. 누락 Core/Scripting 모듈을 있다고 가정하지 않음 | 엔진 역할·gate·ADR Engine Compatibility, 위험 표기·receipt. 기존 맵과 기술 설정을 원본으로 덮지 않음 |
| design-system `1-parse:51–81, 115–122`, `3-file-skeleton:17–27`; design-review `:31–40, 158–165` | minimal/retrofit의 절 수 지시 충돌. 최소 리뷰 표에는 Overview required가 남음 | minimal 자발 GDD는 Summary+5절을 일관 적용. retrofit은 “누락 필수절+사용자가 선택한 추가절”만. 리뷰의 형식 누락은 advisory로 통일 | GDD 양식/guide, gdd-structure 출력 해석, 인덱스 상태·후속 AC |
| design-system `2-gather-context`/`4-section-design`; map-systems Category routing | 트랙·기차가 Gameplay 또는 Core라고만 분류되면 Networking 도메인이 누락될 수 있음 | Category와 별도로 사용 도메인 집합을 기록. 기획 §6–7의 동시 경쟁·복제를 Networking으로 추가 | feasibility, 엔진 참고 선택, ADR domain, full design-review의 network 역할. Category 열 이름은 유지 |
| architecture-decision `3-gather-context:8–48`, `5-generate:222–260`; architecture registry header `:7–23` | registry 없을 때 분기가 부족하고 Proposed ADR stance가 locked 제약으로 읽힐 수 있음 | 부재/빈 목록/유효 엔트리를 구분. `adr:`의 Status를 확인해 Accepted만 구속 제약, Proposed는 충돌 후보로 표시 | ADR author/review, stories/dev-story. 미채택 결정을 구현 규칙으로 굳히지 않음 |
| architecture-decision CONTRACT `:49–52`와 `5-generate:208–220` | CONTRACT는 GDD 수정 금지, 본문은 ADR+GDD 동시 수정 허용 | 권한을 하나로 통일. 추천은 기존 기획/관련 GDD의 별도 diff를 보여 준 명시적 변경 세트만 허용; 단순 ADR 저장에 포함했다고 추정하지 않음 | 문서 변경 범위·registry source·GDD 재검토·변경 영향 기록 |
| architecture-decision `0-accept:53–82`; propagate `:218–229` | 채택으로 모든 연결 스토리 Ready 전환하거나 Superseded 뒤 차단이 자동 완료된 듯 보일 수 있음 | 잔여 ADR/기획 blocker 검사 후 상태 전환. propagate는 차단 대상 스토리 경로를 보고하고 구현 소비자의 상태 재검사를 연결 | create-stories/dev-story/readiness, 작업 경로·상태 helper |
| propagate `:31–35, 49–59, 96–119` | minimal은 선택 ADR가 있어도 무조건 stop. “마지막 해당 파일 변경” 설명과 `HEAD~1..HEAD` 실제 비교가 다름 | 명시 입력/연결 ADR가 존재하면 minimal에서도 선택 범위 영향 검토 허용. GDD뿐 아니라 기획/QuickSpec의 출처 연결도 입력. 마지막 **그 파일 변경 커밋과 부모** 또는 사용자가 지정한 baseline으로 비교 | adr-status/md-section, 기획 경로, traceability, report, manifest 재생성 필요 표시. N−M을 검증된 무영향으로 보고하지 않음 |
| architecture-review `3-traceability:92–127`, `9-handoff:12–18`; create-architecture `8-handoff:39–46` | 기본 Private/Tests·CI·전 화면 UX를 minimal에도 필수처럼 안내. 파일 존재와 실행 통과 혼동 | project.yaml test_root/commands 사용. 현재 tests 없음은 예정. 선택 리뷰 범위 밖 준비물은 권고로 표시. 실행 결과·역할별 2인 증거를 별도 기록 | RTM·QA·스토리 종료·다음 작업 안내. 빌드/2인 검증 미실행 사실 유지 |
| ux-design `sections-hud:99–101`; ux-review `:195–202` | 탐험/전투/대화 상태를 이 게임에 강제 | 준비/주행/과열/성공/실패/연결 중/호스트 종료로 상태표 구성. 일반 예시 상태는 해당 없음 표기 | HUD 양식/guide·작성·리뷰 세 곳 동시 변경. 원문 §3, §7 근거 |
| ux-design `sections-ux-spec:145–175, 196–214`; ux-review `:143–148` | UI Write가 곧 권한 있는 게임 상태 쓰기로 오인될 여지 | 로컬 입력/미리보기·서버 요청·확정/거절 결과·복제 데이터 표시를 나눔. 타이머/HUD는 기존 맵 근거 사용. 동일 조각 선점 거절과 호스트 로컬 갱신의 피드백 AC 포함 | UI 스토리·ui-programmer·네트워크 검토. Steam 친구 초대/로비 선택은 ADR로 남기고 미정 API를 단정하지 않음 |
| reverse-document `:204–218, 228–253`; architecture-doc-from-code `:1–8` | 비정규 ADR 파일명/Status 형태로 생성되어 adr-status 검색·채택 경로에 안 맞음. 코드 관찰이 의도로 오인될 위험 | 향후 사용할 때 출처/커밋/미확인 의도를 유지한 관찰 문서로 저장하거나 정규 Proposed ADR 양식으로 변환 후 accept 절차. 기존 Doc/기획.md와 불일치는 변경 후보로 제시 | adr-status/graph·manifest·stories·리뷰. 자동 Accepted 및 기존 기획 대체 금지 |

프레임워크 맵으로부터 이 게임에 특히 확인할 질문은 다음과 같다. 이는 **설계 검토 항목**이며 현재 구현 결정의 확정이 아니다.

- 서버 선착순 획득/배치의 요청 식별, 거리/소유/배치 유효성, 중복 요청 결과, 두 플레이어에게 보여 줄 실패 피드백 (`기획.md:83–99, 138–155`).
- 진행 중인 기차가 트랙을 통과하는 순간과 회수/재배치 요청이 겹칠 때 서버 처리 순서. “이미 지나간 트랙 비활성화”의 정확한 전환 시점 (`:98–99`).
- 제한 3분에 준비 30초가 포함되는지, 웨이포인트 미통과 시 승패 판정, 모든 웨이포인트를 포함한 완주 가능 경로 생성의 보장 범위. 기존 문구를 넘어서는 답은 미확정으로 남김.
- 서버 전용 규칙과 두 플레이어가 읽는 공유 상태, 로컬 프리뷰/UMG 표시의 분리. 호스트 UI 갱신을 클라이언트 수신 OnRep에만 의존하지 않도록 검토 (`unreal-framework-map.md:12–28, 46, 62–69`).
- Steam 세션 유지/초대/호스트 종료 처리, 접속 가능한 게임 구간·중도 합류 정책. 대상 기획은 호스트 종료 시 전체 종료를 정했지만 다른 정책은 추정하지 않음 (`기획.md:145–155`).

## 7. 변경 영향 기능의 이번 판단

**지금 즉시 원형 이관은 추천하지 않지만, 선택 ADR가 생기면 우선순위가 높아진다.** 이유는 현재 비교할 이전 GDD·채택 ADR가 없기 때문이다. 원형은 설계 변경의 코드 영향 전체를 추적하는 도구도 아니다. 기획 diff→명시 연결 ADR→상태 분류/선택적 Superseded 처리→추적표/보고서까지다(`propagate-design-change:20–35, 208–263`).

현재 요청에서는 다음 수준을 추천한다.

1. M0/M1부터 스토리/스펙에 `Doc/기획.md`의 근거 절과 관련 설계 파일을 남긴다. 아직 중앙 TR가 없어도 연결을 잃지 않는다.
2. 첫 ADR가 생기면 기획 절/QuickSpec/GDD에서 그 ADR로 가는 링크를 기록한다. minimal은 “ADR를 강제하지 않음”으로 유지하되, **존재하고 명시 참조된 ADR는 실제 검토**하도록 propagate를 수정한다.
3. 영향 보고서는 Still Valid/Needs Review/Likely Superseded와 “이번에 확인하지 않은 집합”을 분리한다. 참조 없음은 사유를 기록하고 명시 링크/본문 참조 탐색의 한계도 표시한다.
4. Superseded는 사용자가 대응을 선택한 상태 변경이다. 새 ADR 작성·채택·스토리 갱신·manifest/receipt 갱신·실제 구현·2인 검증이 완료됐다는 뜻이 아니다. 필요한 후속 작업을 경로로 인계한다.

중앙 TR/registry·코드/테스트까지 영향 자동 확장하는 것은 별도 기능 선택이다. TrackBuilding에서 확대를 보류했다는 이유로 TrackBuilding2에 금지하거나, 반대로 현 요청만으로 전체 확대를 확정하지 않는다.

## 8. 이관 순서·완료 확인·미확정 사항

### 8.1 권장 순서

1. **입력 계약 먼저:** 기존 기획·설정·맵을 보존하고 §5.1의 brief·선택 문서·작업 경로를 구현 흐름 담당과 일치시킨다. 먼저 전체 설정 파일이나 기술 문서를 덮어쓰지 않는다.
2. **M0:** brainstorm을 기존 기획에서 brief를 추출하는 모드로 수정한다. 고정 선택은 반복 질문하지 않고 미정 범위·순서만 초안으로 보여 준다. 템플릿과 create-stories/next-step의 같은 brief 경로까지 수정한 변경 세트를 만든다.
3. **작은 변경은 M1:** QuickSpec·스토리 참조를 연결하고 Unreal 실제 수치 원본을 가리키게 한다. 범위가 커지면 선택 GDD로 전환한다.
4. **위험 기능만 D/A/U:** 의존 정리가 필요하면 map-systems, 행동 규칙은 design-system, 기술 선택은 architecture-decision, 화면은 ux-design을 개별 추가한다. 작성→독립 검토→명시 채택 상태를 구분한다.
5. **소비자 연결 확인:** 선택 GDD/ADR/UX/QuickSpec이 minimal 스토리와 구현 입력에 실제 전달되는지 확인한다. 전달되지 않는 문서를 더 만드는 것으로 해결하지 않는다.
6. **증가한 복잡도에만 확장:** 여러 GDD가 생기면 교차 검토, 여러 ADR의 반복 참조가 불편해지면 master architecture/TR/manifest를 선택한다. 기획 변경과 연결 ADR가 생기면 C를 추가한다.

### 8.2 실제 이관의 완료 기준 (이번에 실행하지 않은 후속 확인)

- [ ] 채택한 스킬 파일의 모든 필수·동적 참조가 해소돼 있다. 제외한 모드의 dangling references와 allowed-tools 명령도 정리돼 있다.
- [ ] 기존 `Doc/기획.md`, `CLAUDE.md`, project.yaml 네 값·파생값, framework-map을 덮어쓰지 않았다. technical-preferences 부재를 숨기지 않는다.
- [ ] brief 여섯 필드와 원문 절 연결이 있고, 구현되지 않은 기능을 `[x]`로 만들지 않는다. 기존 목적을 임의 축소하지 않는다.
- [ ] 현재 minimal에서 systems-index/GDD/ADR/TR/manifest/전체 UX/CI·단계 전환을 새 필수 조건으로 요구하지 않는다. 선택 기능을 요청하면 필요한 범위만 작동한다.
- [ ] 선택 ADR를 스토리가 참조하면 Accepted·의존·버전·요약이 소비된다. 미참조 문서는 통과한 것으로 말하지 않는다. accept는 다른 blocker를 남겨 둔 스토리를 Ready로 바꾸지 않는다.
- [ ] 선택 GDD에는 Summary와 tier에 맞는 절·수치·예외·테스트 가능한 AC가 있다. 기존 한국어 개요를 강제로 8절 검사하지 않는다.
- [ ] helper의 경로·glob·ADR 이름 대소문자·registry source/referenced_by·receipt 입력·catalog가 writer와 같다. 빈/미존재/미확인 결과를 분리한다.
- [ ] UX 체크리스트는 실제 라운드/네트워크 상태와 대상 입력을 다룬다. 접근성 tier가 미정이면 NOT ASSESSED, minimal의 pattern library 미채택은 N/A다.
- [ ] `engine.code_root/test_root/commands`는 project.yaml을 따른다. 테스트 파일 연결과 실행 통과를 구분한다. 엔진 사실은 5.8.4 지도/소스 증거 또는 확인 필요로 기록한다.
- [ ] 이관의 정적 계약 확인과 게임 구현·빌드·호스트/클라이언트 검증 완료를 별도로 보고한다. 실제 게임 동작 확인은 이후 구현 작업의 범위다.

### 8.3 미확정 사항

- brief/QuickSpec/GDD/Architecture/UX와 작업 상태 문서의 최종 위치. 본 보고서의 Doc 경로는 추천안이다.
- 첫 coarse Feature 묶음·Build order·당장 연기할 기능. Steam·2인 리슨 서버 목적은 유지한다.
- 선택 ADR를 minimal 스토리에 전달하는 정확한 필드·reader 변경안. 기존 `GDD`/`ADR Version` 계약을 재사용할지 확정 필요.
- 복제 표현, Steam 온라인 API, 중도 합류·동시 행동 세부 규칙 등 실제 기술/기획 선택. 기존 기획으로 확정된 내용과 분리한다.
- 중앙 entities/architecture/TR registry와 manifest가 실제로 필요한 규모가 됐는지. 지금 만들 이유는 없다.
- 원형의 엔진 specialist 위임과 교차 GDD 병렬 검토를 유지할지, 본 세션 렌즈로 재작성할지. `review=solo`와 무관하게 남는 호출을 숨기지 않는다.
- technical-preferences.md의 현재 부재가 의도인지. 이관 시 필요한 미정 항목만 처리하고 원본 기본 문서를 통복사하지 않는다.

### 8.4 이번 보고서의 확인 범위

실파일 목록·원본 내용·기준 커밋·대상 설정/기획/프레임워크 맵·기본 C++ 파일을 읽어 정적 대조했다. 관할 65개 파일의 판정, 외부 의존성, 후속 소비자와의 불일치, 변경안을 기록했다. 이관·엔진 실행·빌드·테스트·커밋은 수행하지 않았다. 기존 참고 보고서·다른 담당 보고서·색인은 수정하지 않는다.
