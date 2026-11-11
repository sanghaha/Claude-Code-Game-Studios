# 06. 자동 세션 복원 선택 이관 검토

작성일: 2026-11-11 (Asia/Seoul)  
상태: **분석·추천안. 실제 이관 결정이나 설치 완료 기록이 아니다.**

## 1. 결론과 최소 이관 묶음

**체크포인트 작성 도우미·템플릿·짧은 SessionStart 복원만 먼저 이관하는 구성을 추천한다.** 원본 훅 전체를 설치할 필요는 없다. Stop 보관과 PreCompact 로그는 선택 기능으로 남긴다. 여기서 세션은 개발 대화 세션이며 Steam 로비·Listen Server 세션이나 게임 저장/로드 기능을 뜻하지 않는다.

| 구분 | 이번 추천 | 현재 최소 경로에서의 이유 |
|---|---|---|
| 지금 추천 | `.claude/hooks/session-start.sh`의 루트 처리·브랜치·CHECKPOINT 복원 부분을 축소 이식 | 새 세션·clear·compact 후 현재 작업과 다음 행동을 보여주는 핵심 |
| 지금 추천 | `.claude/scripts/session-state.sh` + `.claude/docs/templates/session-state.md` | 상태를 작성하는 주체가 있어야 복원도 유효하다. CHECKPOINT는 교체, Notes는 append라는 계약을 공유 |
| 지금 추천 | `.claude/hooks/yaml-helper.sh`를 공용 설정 라이브러리로 보존 | 훅이 호출하는 `session_state_enabled()` 제공. 파일이 hooks 폴더에 있다고 이벤트 훅인 것은 아니다 |
| 운영용 동반 추천 | `.claude/scripts/rotate-session-state.sh` | 당장 복원 자체에는 필수 아님. Notes가 길어졌을 때 명시 실행하는 보존 도구. marker·중단 처리 보완 후 함께 두는 편이 낫다 |
| 조건부 | 축소한 `.claude/hooks/session-stop.sh` | 변경된 상태의 로컬 이력이 실제로 필요할 때. 복원용 active.md를 생성하거나 대화를 요약하지 않는다 |
| 조건부 | `.claude/hooks/pre-compact.sh` | 압축 발생 시각을 조사할 필요가 있을 때만. 체크포인트 저장 기능이 아니다 |
| 이번 단위 제외 | 그 밖의 이벤트 훅, 업데이트·스프린트·버그·코드 상태·에이전트 호출 비용·토큰 관찰 | 자동 복원의 필수 의존성이 아님. 다른 이관 단위에서도 불필요하다는 뜻은 아니다 |
| 전체 복사 제외 | 원본 `.claude/settings.json`, `project.yaml`, `CLAUDE.md`, 기술 문서 | 선택한 이벤트와 필요한 권한만 병합한다. 기존 설정·기술 전제를 덮어쓰지 않는다 |

추천 최소 파일은 위 **5개**(`session-start.sh`, `yaml-helper.sh`, `session-state.sh`, `rotate-session-state.sh`, 템플릿)와 선택 이벤트 설정 조각이다. 여기에 짧은 복구 사용법·작성 책임 문서, Git 제외 규칙·LF 규칙을 필요한 만큼 병합한다. `rotate-session-state.sh`의 **설치 추천**과 실제 회전의 **조건부 명시 실행**을 구분한다. 이 5개를 원본 그대로 복사하라는 뜻은 아니며 §7의 변경이 선행되어야 한다.

원본 `production/session-state/active.md`나 `production/session-logs/` 내용은 가져오지 않는다. 대상의 첫 체크포인트는 대상의 실제 작업에서 새로 작성한다. 원본 프로젝트의 이력을 복원하면 잘못된 작업·브랜치를 현재 상태로 오인할 수 있다.

## 2. 확인 범위와 대상 전제

### 2.1 근거 표기와 조사 방법

- **S** = `C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios`
- **T** = `C:/Users/Sangha_Remote/Documents/TrackBuilding2`
- 이하 `S/.claude/...:행`은 위 원본 절대 경로 아래 실제 파일의 행 번호다. 행 범위는 이번에 읽은 기준 커밋의 파일에 대응한다.
- 원본 HEAD는 `abc0de79d6ceb0b5d7aa693945849b3937afbe6a`로 확인했다. 관할 훅·스크립트·설정·템플릿의 `git diff HEAD`는 비어 있었다. 원본 `ax-setup/`의 기존 미추적 자료는 수정하지 않았다.
- 두 루트의 숨김 파일을 포함한 `AGENTS.md` 검색과 공통 상위 경로(`C:/`, `C:/Users/`, 사용자 홈, `Documents/`) 확인에서 적용할 `AGENTS.md`는 발견하지 못했다. 원본·대상 `CLAUDE.md`와 원본의 관련 참조 지침을 읽었다. 원본의 일반 승인 문구보다 이번 보고서 작성까지 승인한 세션 지시를 우선했다.
- 핵심 복원 스크립트·템플릿 본문, 이벤트 등록, 나머지 모든 훅·스크립트의 목적·호출·상태 경로를 정적으로 대조했다. 원본 `SKILL.md`는 작성자·소비자를 확인하는 **분석 자료**로 읽었으며 스킬을 실행하지 않았다.
- `S/ax-setup/ccgs-adoption-reference.md` §5·7·9와 `T/ax-setup/ccgs-settings-skill-impact.md` §C 및 동반 JSON을 참고한 뒤 원본 구현으로 대조했다. JSON의 method 자체도 완전한 의존성 폐쇄나 실행 추적이 아닌 정적 색인이라고 명시한다. 기존 TrackBuilding 비교 판단을 TrackBuilding2의 채택 결정으로 승격하지 않았다.

### 2.2 변경하지 않는 전제

| 대상 사실 | 확인 근거와 해석 |
|---|---|
| 폴더명 TrackBuilding2 / 프로젝트·타깃·모듈명 TrackBuilding | `T/CLAUDE.md:6–7,16,24–25`; 실제 `TrackBuilding.uproject`, `Source/TrackBuilding.Target.cs`, `Source/TrackBuildingEditor.Target.cs` 존재 |
| Unreal 5.8.4 / C++ 기반 + Blueprint / Steam PC / Listen Server 2인 | `T/CLAUDE.md:3–17`, `T/project.yaml:18–29`, `T/Doc/기획.md:7–10,142–149` |
| stage=Concept, goal=hobby, rigor=minimal, automation=collaborative | `T/project.yaml:6–16`. 이 보고서가 바꾸지 않는다 |
| workflow=minimal, density=terse, QA=minimal, story_granularity=coarse, review=solo, team=individual | 원본 `yaml-helper.sh:1251–1254`의 minimal 확장과 대상 영향 JSON을 대조. 여섯 파생값을 명시 키로 고정하지 않는다 |
| 코드·테스트 루트 | `T/project.yaml:23–25`: `Source/TrackBuilding`, 예정 `Source/TrackBuilding/Tests`. 후자는 현재 폴더 없음. 존재 여부로 stage를 올리거나 테스트 성공을 추정하지 않는다 |
| 실행·검증 사실 | `T/project.yaml:38–44`, `T/CLAUDE.md:43–45`: 명령 저장과 실제 성공은 별개. 구현·빌드·2인 플레이 검증 성공은 확인하지 않았다 |
| 기술 문서 | `T/.claude/docs/unreal-framework-map.md`는 존재하며 대상 `CLAUDE.md:34–39`가 참조한다. 요청에서 읽기 자료로 지정된 `T/.claude/docs/technical-preferences.md`는 이번 최종 재조회에서 **존재하지 않았다**. 내용을 추정·복구하거나 원본 템플릿으로 새로 채우지 않는다 |
| 설치 상태 | 현재 대상 `.claude/`에는 `docs/unreal-framework-map.md`만 확인됨. 대상 hooks/scripts/settings.json은 없다. 보고서만 저장하며 그 상태를 바꾸지 않는다 |

공통 프로젝트 지침은 이미 완료된 범위로 취급한다. 이 단위는 기존 `CLAUDE.md`, `project.yaml`, 기획·프레임워크 맵을 재작성하는 작업이 아니다.

## 3. 원본 동작과 전체 도입 대비

### 3.1 실제 복원 경로

| 시점 | 원본의 실제 동작 | 복원에서의 의미 |
|---|---|---|
| 작업 도중 | 스킬/작업자가 `session-state.sh checkpoint`에 본문을 전달한다. helper가 템플릿 생성·블록 교체를 수행 | 내용의 의미·최신성은 작성자의 책임. helper가 대화를 해석하지 않는다 (`session-state.sh:14–42,144–175`) |
| SessionStart | Git·리뷰·검증·업데이트·스프린트·버그·코드 관찰 후 `active.md` CHECKPOINT를 출력 | 실제 자동 주입은 이 이벤트. `settings.json:179–195`, `session-start.sh:55–277,279–379` |
| PreCompact | feature 확인 뒤 `compaction-log.txt`에 시각 한 줄 | stdout 없음, active.md 저장·교체 없음 (`pre-compact.sh:33–54`) |
| compact 이후 | 원본은 다시 발생하는 SessionStart에서 상태를 보여주도록 설계 | PreCompact 출력에 복원을 기대하지 않는다. 이벤트 발생/전달은 대상 클라이언트에서 별도 확인 필요 |
| Stop | **응답마다** 변경된 active.md 전체를 보관하고 Git 요약·호출 수를 기록 | 프로세스 종료 시 한 번만 실행하는 훅이 아니다. active.md를 삭제하지 않는다 (`session-stop.sh:33–40,58–89,115–175`) |
| 명시 회전 | CHECKPOINT 종료 줄까지 남기고 이후 Notes를 날짜별 로그로 이동 | 어떤 훅도 자동 실행하지 않음 (`rotate-session-state.sh:12–24`) |

`S/.claude/docs/hooks-reference/hook-input-schemas.md:125–146,177–186`의 이벤트 설명과 출력 제한은 **원본이 기록한 관찰/계약**이다. 현재 설치된 Claude Code나 Codex에서 검증한 제품 사실로 보고하지 않는다. 특히 `.claude/settings.json`을 두는 것만으로 현재 Codex가 이 이벤트를 실행한다고 볼 근거는 없다. Claude Code를 사용할지, 다른 실행기의 대응 수단을 사용할지는 이관 전에 확정해야 한다. 그 전에도 같은 파일을 수동으로 읽고 갱신하는 방식은 가능하다.

### 3.2 구성 비교

| 항목 | 원본 전체 훅·설정 도입 | 추천 최소 복원 |
|---|---|---|
| 이벤트 | SessionStart 2개 명령 + 도구 검사·알림·PreCompact·Stop·Subagent 이벤트 | SessionStart 1개 명령. Stop/PreCompact는 필요할 때만 추가 |
| 파일 범위 | hooks 18개 중 settings에 등록된 이벤트 명령 15개 + helper/진단 + scripts의 조건부 의존성 | 핵심 5개 + 복구 설명·설정 조각. scripts 54개 전체가 필요하지 않음 |
| 시작 비용 | 리뷰 설정·enum 검증(Python), Git 이력, 코드 검색, 버그 집계, 업데이트 캐시/백그라운드 갱신 등 | 브랜치와 짧은 현재 상태. 전체 설정 해석·코드/문서/에셋 검색·네트워크 없음 |
| 저장 | active.md, 전체 보관 로그, Git 활동, 압축 로그, 에이전트 감사·비용 | active.md와 사용자가 명시한 Notes 회전. 선택 시 축소 Stop 이력 |
| Unreal 적합성 | 여러 엔진용·CCGS 문서 경로·legacy 참조를 함께 점검해야 함 | `TrackBuilding` 경로와 실제 검증 상태만 상태 본문에 기록 |
| 운영 영향 | 권한·deny·statusLine·worktree 정책까지 같이 들어올 위험 | 기존 설정에 채택한 이벤트만 병합 |

전체 구성은 후속 단위에서 검사·알림·에이전트 감사까지 별도로 채택할 때 재평가한다. 자동 복원의 선행 조건으로 강제하지 않는다.

## 4. hooks 파일별 판정 — 18개 전부

아래 경로는 모두 `S/.claude/hooks/` 기준이다. **제외는 이번 이관 목록에서 제외한다는 뜻이며 원본 삭제 지시가 아니다.**

| 파일 | 판정 | 근거 / 현재 최소 경로에서의 필요성 |
|---|---|---|
| `session-start.sh` | 추천·축소 수정 | `:24–31` 루트, `:57–68` Git, `:279–379` 복원. 중간의 일반 운영 관찰은 제거. 현재 복원의 유일한 자동 출력 소비자 |
| `yaml-helper.sh` | 추천·공용 라이브러리 | `:793–815` feature 판단, `:1374–1417` 일반 설정 해석. 이벤트 등록 대상이 아님. 필요한 함수만 호출하며 공용 인터페이스를 임의 삭제하지 않음 |
| `session-stop.sh` | 조건부·축소 수정 | `:76–89` 변경 상태 보관만 선택. `:54–56,91–113` Git 요약, `:115–175` 호출 비용은 최소에서 제외 |
| `pre-compact.sh` | 조건부 | `:33–52` 압축 시각 로그뿐. 없어도 active.md → SessionStart 복원은 성립 |
| `detect-gaps.sh` | 이번 단위 제외 | `:33–36,71–104,141–173,208–364` 온보딩·기획/코드 간극·단계 관찰. 복원 아님. `Doc/기획.md`를 원본 design 경로 부재와 혼동할 여지도 별도 조정 대상 |
| `log-agent.sh` | 이번 단위 제외 | `:33–48,73–90` feature를 같이 읽지만 에이전트 감사 기록. 호출 수와 복원은 별개 |
| `log-agent-stop.sh` | 이번 단위 제외 | `:33–48,73–87` 완료 감사 기록. CHECKPOINT 작성/복원 없음 |
| `log-instructions.sh` | 이번 단위 제외 | `:35–68,70–135` 규칙 로딩 진단, feature와 독립. **원본 settings에 등록되지 않음**. 필요 시 local 진단용이라는 `hooks-reference.md:66`과 일치 |
| `update-check.sh` | 이번 단위 제외 | `:24–45,95–120,132–141` CCGS 1.3.0/GitHub 업데이트·사용자 캐시·curl/wget. 시작 훅에서 source 호출까지 제거하여 숨은 백그라운드 네트워크 의존성 해소 |
| `notify.sh` | 이번 단위 제외 | `:33–79` permission/idle 알림과 Windows 풍선 도움말. 복원과 무관 |
| `validate-agent-artifact.sh` | 이번 단위 제외 | `:33–55,86–105` 위임 산출물 존재 경고, `write-guard.sh artifact` 소비. 체크포인트 누락을 고치는 기능 아님 |
| `validate-assets.sh` | 이번 단위 제외 | `:33–47,92–117,148–170` 에셋/JSON 검사. Unreal Content 경로 지원 여부와 복원은 별개 |
| `validate-commit.sh` | 이번 단위 제외 | `:33–42,615–650` 커밋·JSON·설계/코드 점검. Git 해시 사용만으로 이 훅이 복원 의존성이 되지 않음 |
| `validate-push.sh` | 이번 단위 제외 | `:33–41,212–245` push/tag/merge/deploy 경고. 브랜치 복원 경고와 다른 기능 |
| `validate-read.sh` | 이번 단위 제외 | `:15–29,81–109` 큰 파일 Read 제한. session-state 폴더도 감시하지만 복원 기능 자체의 선행 조건은 아님 |
| `validate-registry.sh` | 이번 단위 제외 | `:33–44,69–100` 레지스트리 비교, `hook-input.sh`/`registry.sh` 의존. 상태 복원과 무관 |
| `validate-skill-change.sh` | 이번 단위 제외 | `:33–40,98–108` 스킬 변경 후 static 점검 안내. 복원과 무관 |
| `validate-write.sh` | 이번 단위 제외 | `:33–44,105–125` 쓰기 정책 관찰, `hook-input.sh`/`write-guard.sh` 의존. checkpoint 쓰기 계약은 별도로 맞춰야 하나 전체 검사 설치는 필수 아님 |

## 5. scripts 파일별 판정 — 54개 전부

아래 경로는 모두 `S/.claude/scripts/` 기준이다. 제외 파일의 행은 실제 목적·호출자를 확인한 위치다. 다른 기능에서의 채택 여부를 결정하는 표가 아니다.

| 파일 | 이번 단위 판정 | 근거 / 최소 복원에서의 필요성 |
|---|---|---|
| `adr-dep-graph.sh` | 제외 | `:2–17` ADR 의존 그래프·순환 점검. 상태 주입 의존성 없음 |
| `adr-status.sh` | 제외 | `:2–19,88` ADR 상태·섹션 및 ADR 템플릿 조회 |
| `agent-check.sh` | 제외 | `:2–18` 역할·위임 문서 정적 점검 |
| `artifact-check.sh` | 제외 | `:2–17,85` workflow-catalog 산출물 존재 관찰. 복원에 전체 워크플로 카탈로그 불필요 |
| `asset-scan.sh` | 제외 | `:2–19` 에셋 이름·크기·참조 조사 |
| `bug-stats.sh` | 제외 | `:2–15` 버그 집계. 원본 시작 훅의 유일한 직접 scripts 실행(`session-start.sh:183–185`)을 삭제하면 연결 해소 |
| `code-markers.sh` | 제외 | `:2–16` TODO/FIXME 등 코드 지표. 시작 훅 자체의 grep 관찰도 함께 제거 |
| `compat-inventory.sh` | 제외 | `:2–17` Claude Code 전용 문법 목록 도구. 이관 조사에 쓸 수 있어도 런타임 의존성 아님 |
| `config-set.sh` | 제외 | `:2–16` project.yaml/local 수정 도구. 복원 도입을 위해 기존 설정을 다시 저장할 필요 없음 |
| `content-count.sh` | 제외 | `:2–20` 계획 대비 콘텐츠 수 집계 |
| `cut-apply.sh` | 제외 | `:2–15` 기능 범위·스토리 변경 도구 |
| `cut-read.sh` | 제외 | `:2–15` 범위 조정 화면용 brief·마감·체감 기록 조회 |
| `debt-register.sh` | 제외 | `:2–18` 기술 부채 번호·산술·조회 |
| `door-in.sh` | 제외 | `:2–15` 브라우저 게임 원본 복사/검증. 자체 Git checkpoint는 이번 active.md 복원과 다름 |
| `engine-row.awk` | 조건부 의존성·최소에서는 제외 | `:1–14` 엔진 표 조회. yaml-helper의 `resolve_code_root` 등 엔진 함수까지 유지·사용하면 `engines.tsv`와 동반. `session_state_enabled()`는 사용하지 않음 |
| `flaky-stats.sh` | 제외 | `:2–15` 테스트 실행 이력의 불안정성 집계 |
| `game-read.sh` | 제외 | `:2–12` 브라우저 게임 역문서화 읽기 |
| `gdd-structure-check.sh` | 제외 | `:2–15,25–26` 시스템 GDD 섹션 존재 검사 |
| `godot-parse-check.gd` | 제외 | `:1–15` Godot 로드/파싱 검사. Unreal 복원에 불필요 |
| `hook-input.sh` | 제외 | `:2–15` Read/Write/Edit payload 및 Edit 재구성. 축소 복원은 이 payload를 해석할 필요 없음 |
| `inputs-check.sh` | 제외 | `:2–15` 기능 입력 존재/빈 내용 분류. 작은 복원 경로는 직접 필요한 파일만 확인 |
| `itch-zip.sh` | 제외 | `:2–15` Web itch 배포 압축 |
| `json-flat.awk` | 제외 | `:1–14` package.json용 JSON 평탄화. 이벤트 payload 전체 파서로 전용하지 않음 |
| `md-section.sh` | 제외 | `:2–15` 일반 Markdown 섹션 편집. CHECKPOINT marker 처리는 session-state.sh가 직접 수행 |
| `migrate-v1-config.sh` | 제외 | `:3–15,50` v1 설정·legacy 문서 이관. 이미 있는 대상 project.yaml 재생성 수단으로 사용하지 않음 |
| `one-shot-check.sh` | 제외 | `:2–19` 브라우저 one-shot 실행·스크린샷·체크 기록 |
| `one-shot-driver.js` | 제외 | `:1–15` 브라우저 입력/시간·랜덤 재현 드라이버 |
| `one-shot-grow.sh` | 제외 | `:2–15` one-shot을 Web 프로젝트로 확장 |
| `one-shot-new.sh` | 제외 | `:2–13` Web one-shot 폴더/템플릿 생성 |
| `one-shot-run.sh` | 제외 | `:2–13` headless 브라우저 재생 |
| `one-shot-share.sh` | 제외 | `:2–13` one-shot 공유 빌드 복사 |
| `one-shot-tune.sh` | 제외 | `:2–18` Web config.js 수치·체감 로그 수정 |
| `page-shot.sh` | 제외 | `:2–13` headless 브라우저 화면 캡처 |
| `plan-math.sh` | 제외 | `:2–15` 날짜·스프린트 진척 산술. minimal 복원에 sprint-status 불필요 |
| `project-coherence.sh` | 제외 | `:3–13` 설정과 엔진 산출물 일치 조사. 자동 복원 선행 조건 아님 |
| `project-state.sh` | 제외 | `:2–15,27–29` 프로젝트 상태·단계 추론. 복원은 실제 작업 상태를 읽고 Concept를 유지 |
| `read-guard.sh` | 제외 | `:2–13,133` 큰 문서 읽기 정책. 상태 경로를 감시하지만 이벤트 복원 출력과 별개 |
| `references-save.sh` | 제외 | `:2–12` Claude 대화 로그에서 첨부 이미지 추출. 대화 세션을 다뤄도 작업 checkpoint 복원 기능은 아님 |
| `registry.sh` | 제외 | `:2–13` 엔티티/TR-ID/아키텍처 레지스트리 |
| `review-receipts.sh` | 제외 | `:2–13,42–51` 리뷰 내용 해시. Stop이 같은 git hash-object를 직접 사용할 뿐 이 파일을 호출하지 않음 |
| `review-scope.sh` | 제외 | `:2–13` 증분 GDD 리뷰 범위 |
| `rotate-session-state.sh` | 운영용 동반 추천·수정 | `:12–24,47–63,85–163` 명시적 Notes 보관. 최초 복원의 하드 의존성은 아님 |
| `session-state.sh` | 추천·수정 | `:14–44,51–55,144–207` 작성·show·append. 템플릿과 함께 필요 |
| `set-stage.sh` | 제외 | `:2–13` YAML/legacy 단계 이중 쓰기. 복원에서 단계 변경 금지 |
| `skill-lint.sh` | 제외 | `:2–17` 스킬 정적 관찰. 이관 후 별도 점검에 쓸 수 있어도 복원 런타임 의존성 아님 |
| `story-facts.sh` | 제외 | `:2–14` Story 내용/준비 상태 조회. dev-story 채택 단위의 별도 의존성 |
| `story-status.sh` | 제외 | `:2–13` minimal Story 순서. next-step/story-done을 통째로 가져오면 필요하지만 단순 복원은 현재 next step을 읽으면 됨 |
| `test-run.sh` | 제외 | `:2–15` 엔진 자동 테스트 실행. 복원 이벤트에서 엔진 실행 금지 |
| `test-scaffold.sh` | 제외 | `:2–14` 테스트/CI 골격 생성. 예정 Tests 폴더를 이 기능 때문에 만들지 않음 |
| `vendor-three.sh` | 제외 | `:2–13` Three.js 다운로드·번들링 |
| `web-detect.sh` | 제외 | `:2–13` Web 설정 탐지 |
| `web-scaffold.sh` | 제외 | `:2–13` Web 프로젝트 골격 생성 |
| `write-guard.sh` | 제외 | `:2–17,374–376` 일반 쓰기 규칙. active.md 예외가 있으나 helper 자체의 의존성 아님. 향후 검사 단위와 체크포인트 교체 계약만 정합화 |
| `yaml-get.awk` | 최소에서는 제외 | `:1–13` 다른 scripts가 공유하는 스칼라 reader. 현재 session-state/rotate는 YAML을 읽지 않고, session_state_enabled는 자체 awk. 이름이 비슷해도 yaml-helper의 일반 Python 파서가 이 파일을 호출하는 구조가 아님 |

## 6. 실제 동반 자료·작성 역할·조건부 의존성

### 6.1 기본 묶음의 의존성

| 구성 | 직접 필요한 것 | 필요하지 않은 것 / 동적 분기 |
|---|---|---|
| 축소 SessionStart | Bash·Git(브랜치), 표준 sed/grep/awk/wc 등, 공용 yaml-helper의 feature 함수, active.md | 버그·sprint·milestone·코드 지표·엔진 명령·업데이트 호출 없음. 상태 파일 없으면 짧게 미존재를 알리고 종료 |
| `session-state.sh` | Bash/awk, mktemp/cmp/cat 등; 파일이 없을 때 `.claude/docs/templates/session-state.md` | Python·jq 없음. `--root`를 주지 않으면 **스크립트가 속한 저장소**가 루트(`:74–78`); 원본 helper를 대상으로 오인하여 실행하지 말 것 |
| `rotate-session-state.sh` | Bash process substitution, grep/head/tail/cmp/mktemp/mv/date; active.md; 회전 시 로그 폴더 | `--root` 지원 없음(`:26–39`). 이관 후 대상 안의 스크립트를 명시 실행. hook 자동 호출 없음 |
| `yaml-helper.sh`의 feature 함수 | `session_state_enabled()` → `_yaml_helper_set_root()` + 자체 awk | 일반 resolve_config를 호출하지 않으면 Python·엔진 표를 실행 의존성으로 만들지 않음 |
| 공용 yaml-helper 전체 API를 다른 단위에서 사용 | Python 3(`python` → `python3` → `py`, 실행 가능·major 3 확인 `:78–103`), 필요 시 기술 문서 fallback | PyYAML 불필요. 엔진 root/engine_row 기능은 `S/.claude/engines/engines.tsv` + `engine-row.awk`를 동적으로 사용(`:1426–1440`). 이 둘은 feature 함수만 쓰는 최소 복원에서는 불필요 |
| 선택 Stop | Git hash-object, snapshot·로그·해시 파일 | `review-receipts.sh` 호출 없음. 비용 tally를 제거하면 jq·agent-audit·stdin·timeout 읽기 분기까지 불필요 |
| 선택 PreCompact | feature 함수 + date + 로컬 압축 로그 | transcript·대화 분석·최신 상태 생성 없음 |

`yaml-helper.sh`를 함께 둔다고 `engines.tsv` 등이 모든 경우에 하드 의존성이 되는 것은 아니다. 다만 **공용 파일이 있다고 전체 API가 지원되는 것처럼 안내하지 말아야 한다.** 다른 단위가 `resolve_code_root`, `resolve_test_root`, `resolve_config ... engine_row` 등을 채택한다면 그 단위에서 엔진 표와 AWK를 함께 가져와야 한다. 원본 Unreal 행은 code_root=`Source`, test_root=`Source/<Module>/Private/Tests`, asset_root=`Content`, 명령은 `-`이다. 대상의 명시된 `Source/TrackBuilding` 및 예정 `Source/TrackBuilding/Tests`, PowerShell 명령이 우선하며 표의 placeholder로 바꾸지 않는다.

### 6.2 설명 문서와 템플릿

| 자료 | 추천 처리 |
|---|---|
| `S/.claude/docs/templates/session-state.md:29–44,57–76` | STATUS/CHECKPOINT marker·필드 계약을 보존한 대상용 템플릿. 프로젝트/파일 예시를 TrackBuilding에 맞춤 |
| `S/.claude/docs/context-management.md:5–49,147–172` | 파일 기반 복원·갱신 시점·중단 재개·수동 복원 부분만 짧게 이식. 원본의 전체 컨텍스트 운영/위임 가이드는 이 단위 제외 |
| `S/.claude/docs/setup-requirements.md:14–17,48–59,65–81` | Git Bash·LF·필요 Python 범위와 미설치 시 동작을 채택 구성에 맞춰 설명. 전체 훅이 있다는 원본 문구는 유지하지 않음 |
| `S/.claude/docs/hooks-reference.md:14–20,59–66` 및 `hooks-reference/hook-input-schemas.md:125–146,177–186` | 채택 이벤트와 출력 계약 확인용. 15개 훅 설명을 통째로 사용자 운영 문서로 넣지 않음 |
| `S/.claude/docs/config-resolution.md:107` 및 `:294–299` | 공용 설정 라이브러리를 쓰는 범위의 해석·local whitelist 참고. 시작마다 전체 설정 블록을 출력하지 않음 |
| `S/.claude/docs/effects-map.md:1999–2074` | feature on/off 의미 참고. 원본의 호출 비용·감사까지 포함한 설명은 축소 구성과 다르므로 채택 범위 문서를 따로 명확히 함 |
| 대상 `project.yaml`, `CLAUDE.md`, `Doc/기획.md`, `unreal-framework-map.md` | 읽기 전제. 기존 내용을 덮어쓰거나 CCGS 기본 기술 문서로 대체하지 않음 |

### 6.3 작성자와 소비자 — 역할 전체 이관을 강제하지 않음

- 복원 훅은 작성자가 아니다. **현재 작업을 맡은 주 에이전트/사용자 승인 하의 작업 스킬 하나가 checkpoint 작성 책임**을 진다. 의미 있는 변경·결정·검증 결과·중단/차단 후 교체한다. Notes는 필요한 이유·시도만 append한다. 동시 작성은 직렬화한다.
- 원본 `/dev-story`는 모든 결과에 checkpoint를 교체하고, INCOMPLETE이면 재개를, BLOCKED이면 해소 행동을 다음 단계로 쓴다(`S/.claude/skills/dev-story/SKILL.md:211,336–364`). `/story-done`도 모든 판정에 교체한다(`.../story-done/SKILL.md:391–402`). 이 계약은 차용할 가치가 있으나 두 스킬의 전체 파이프라인은 복원의 선행 조건이 아니다.
- `/next-step`은 최소 모드에서도 STATUS/CHECKPOINT를 읽는다(`.../next-step/SKILL.md:102–117`). 이 스킬 전체를 가져오면 workflow-catalog, story-status, artifact-check 등 다른 의존성이 생긴다. 복원만을 위해 가져오지 않는다.
- `S/.claude/agents/lead-programmer.md:1–8`에는 tech-debt 스킬·project 메모리가, `S/.claude/agents/unreal-specialist.md:1–8`에는 별도 역할 도구가 있다. **어느 역할 파일도 이 복원 스크립트가 호출하지 않는다.** 실제 Unreal 작업 역할 선택은 담당 단위에서 결정하며 여기서는 추가 역할 이관 0개를 추천한다.
- 원본의 art-director/audio-director/game-designer/systems-designer 등의 `active.md` append 지침은 Notes 이력 작성이지 CHECKPOINT 최신화를 보장하지 않는다(예: `S/.claude/agents/game-designer.md:45`, `systems-designer.md:43`). 해당 역할을 나중에 채택하면 작성 완료 후 최신 checkpoint를 주 작성자에게 넘기도록 맞춘다.
- `S/CLAUDE.md:50–52`, `S/.claude/rules/agent-memory.md:8–13`은 승인 예외를 **append**로 표현하지만 새 helper·dev-story는 checkpoint를 **교체**한다. `write-guard.sh:374–376`의 경로 예외는 더 넓다. 자동 복원 도입 시 승인된 작업에 수반되는 checkpoint 교체/Notes append 범위를 소비 스킬에 명시해 이 불일치를 해소하는 것을 추천한다. 기존 공통 지침을 이번에 재작성하지 않으며, 과거 checkpoint의 문장을 새 범위의 승인 증거로 사용하지 않는다.

## 7. 이관 전 변경 명세

아래는 **원본을 수정했다는 기록이 아니라 대상용 이식본의 변경 제안**이다. 원본 위치·문제·변경·영향을 구분했다.

| 원본 위치 | 현재 문제/범위 | 대상용 변경안 | 소비자 영향 |
|---|---|---|---|
| `session-start.sh:70–122` | 리뷰·enum 검증은 복원과 별개이며 Python을 시작 경로에 넣음 | 최소에서는 리뷰 배너/전체 enum 검증을 제거. 공용 설정 해석은 필요한 스킬이 호출. feature 오타 진단은 짧게 별도 처리 | 일반 설정 해석 API는 유지. 현재 minimal/solo를 명시 override로 저장하지 않음 |
| `session-start.sh:124–135` | update-check가 캐시를 읽고 필요 시 백그라운드 GitHub 요청 시작 | source와 호출 블록을 제거하고 `update-check.sh`를 목록에서 제외. 설정에 `updates.check=false`를 새로 넣어 우회하는 방식은 불필요 | curl/wget·캐시·CCGS 업그레이드 안내 의존성 제거 |
| `session-start.sh:137–189` | legacy 단계 합의·sprint/milestone·bug-stats 관찰 | 해당 블록과 bug-stats 호출 제거 | stage.txt/review-mode.txt, production/sprints·milestones, tests/playtest를 복원 때문에 만들지 않음 |
| `session-start.sh:191–277` | 코드 grep·엔진 참조 import 경고가 복원보다 먼저 실행. 기존 대상은 원본 ENGINE-REFERENCE-IMPORT 체계를 쓰지 않음 | 코드 지표·resolve_code_root·원본 엔진 import 검사를 제거. 상태를 먼저 출력 | `Source/TrackBuilding` 대형 검색, 엔진 문서 재생성, setup-engine 재실행 제안 방지 |
| 세 훅 `:24–31`; `yaml-helper.sh:478–485`; helper `session-state.sh:74–78`, rotate `:26–27` | cwd·환경·스크립트 위치의 우선순위가 다름. 훅은 cd 실패를 무시하고 계속할 수 있음 | 선택 루트를 정규화하고 올바른 대상임을 확인. 실패하면 그 위치에 쓰지 않고 명시 진단. 상위 폴더를 무작정 탐색하지 않음. 대상 경로와 `.uproject`명 구분 | 중첩 프로젝트 우선 규칙을 훼손하지 않음. 원본/대상 오작동 및 `.claude/production/` 생성 방지 |
| `yaml-helper.sh:793–815,535–560` | `off`만 비활성. false·오타는 enabled; 자체 AWK는 `features:` 블록형만 탐지. inline map/헤더 주석 등 일반 YAML과 판독이 다를 수 있음 | `on/off`와 지원 블록형을 문서화. 잘못된 값/지원하지 않는 표기를 관측하면 경고하고 유실을 막는 기본 on 유지. 일반 resolver와 일관화가 필요하면 공용 소비자 영향 확인 후 공통 함수로 변경 | local → base → 기본 on 유지. Python 없는 최소 경로와 전체 설정 Python 경로를 구분. 임의로 false=off로 의미 변경하지 않음 |
| `session-start.sh:314–371`; `session-state.sh:83–105,145–174`; rotate `:47–63` | marker 검사가 불일치. 시작은 opener만 있고 closer 없을 때만 명시 거부, helper는 첫 유효 쌍 중심, rotate는 closer 하나만 있으면 분리 | 세 소비자가 독립 줄의 정확한 marker **각 1쌍·순서**를 동일하게 검증. 중복·역순·짝 누락은 경고/쓰기·회전 거부. 설명 문장 속 marker는 계속 무시 | 잘못된 파일을 narrative/완료 상태로 복원하지 않음. 기존 무 marker 파일은 수동 검토 후 helper의 삽입 경로로 이전 |
| `session-start.sh:316–330,352–378`; `session-state.sh:55,175` | 출력만 25줄/3000 길이 제한; 파일 전체 추출/스캔은 남음. writer는 25줄 경고뿐. 무 marker fallback 첫20줄에는 문자 제한이 없음 | 정상·fallback·오류 모두 짧은 총출력 상한 적용. 25줄/3000 단위를 문자 또는 UTF-8 byte 중 하나로 명시하고 작성/읽기 일치. 긴 Notes 전체를 변수로 적재하지 않고 첫 유효 블록까지만 읽음 | 한국어 token 수를 '~750'으로 보장하지 않음. 초과 상태는 전문 경로를 안내하며 완전 복원으로 오인하지 않음 |
| `session-state.sh:200–205` | 임시 완성본을 만들지만 최종 `cat > active.md`는 제자리 절단 쓰기. 중단 시 부분 파일 가능, lock 없음 | 일반 파일만 지원할지 결정하고 동일 디렉터리 원자 교체+공통 잠금/이전본 보존 권장. symlink/hardlink를 유지해야 한다면 별도 계약·검증 필요 | 원본의 링크/모드 보존 의미가 달라질 수 있어 조용히 변경하지 않음. show/쓰기/Stop/rotate가 같은 snapshot을 사용하도록 조정 |
| `session-state.sh:176–198` | append는 말미 중복만 방지하고 기존 checkpoint marker 완전성을 검사하지 않음 | append 전에도 상태 구조 점검. 마지막 블록 동일 중복 방지는 유지 | Notes append가 깨진 구조를 계속 키우는 것 방지. 전체 이력 중복 제거를 제공한다고 주장하지 않음 |
| rotate `:54–69,72–75,85–163` | closing만 검사. 보관 성공 후 active 교체 실패 시 양쪽 사본이 남음. 생성 Notes 자체가 5줄을 넘어 재회전 가능. 주석은 filesystem 날짜라고 하나 실제 `date` 사용 | 공통 marker 검사·공통 잠금·snapshot hash/generation으로 재실행 중복 검출. 생성 안내만 있는 Notes는 no-op. 날짜 출처 주석을 실제 실행 시각/시간대로 정정 | 원본의 '보관 성공 전 active 미변경' 순서는 보존. 실패 시 두 사본을 남기고 성공으로 보고하지 않음 |
| Stop `:54–56,91–175` | 8시간 Git 활동·변경 파일명 요약·에이전트 비용까지 포함. stdin에 timeout이 없으면 cat가 무한 대기할 수 있음 | 선택 도입 시 이 블록 전부 제거하고 active snapshot 보관만 남김 | bug/sprint/agent-audit/jq·stdin 처리 불필요. Git 요약을 게임 검증 결과로 오인하지 않음 |
| Stop `:79–87,95–111` | append 성공 여부와 관계없이 후속 hash write 실행 가능. hash 산정 후 파일이 바뀌면 보관한 bytes와 hash 불일치. 비동기 중첩도 잠금 없음 | 잠금 아래 snapshot을 한 번 잡아 그 bytes를 hash·append. 로그 쓰기 성공 확인 후에만 hash 갱신. 실패는 진단 파일/종료 상태에 남김 | 같은 내용 반복 보관 방지는 유지하되 실패한 보관을 성공으로 기억하지 않음. 빈 hash일 때 중복을 허용하는 유실 방지 방향 유지 |
| `settings.json:279–290`; Stop `:36–40` | async Stop은 원본 설명상 timeout을 강제하지 않고 headless teardown 때 중단 가능 | 기본 최소에서 미등록. 이력이 필요하면 짧은 동기 Stop을 우선 검토하거나 별도의 bounded·내구성 계약을 갖춘 async로 검증 | 마지막 보관 성공을 session 종료만으로 보장하지 않음. 복원 원본은 계속 active.md |
| 템플릿 `:36–43,70–76`; dev-story `:340–349` | Godot 예시·모호한 Run result 한 줄로 Unreal 빌드/2인 검증이 합쳐질 수 있음 | 프로젝트=`TrackBuilding`, 파일=`Source/TrackBuilding/...`, 설계=`Doc/기획.md`. build/Automation/호스트/클라이언트 결과·증거를 짧게 구분. 미실행=`NOT VERIFIED`, 중단=`INCOMPLETE` | `Run result`·Current task·Next step·Branch 이름을 유지해 나중의 dev-story/story-done 소비자 호환. QA minimal이라고 실행 성공을 추정하지 않음 |
| `settings.json:3–177,196–266,292–321` | statusLine·worktree·넓은 allow/deny·도구/알림/agent 훅이 복원 설정과 한 파일에 있음 | 기존/후속 설정과 JSON 키 단위로 병합. 복원 때문에 전체 permissions나 statusLine을 덮어쓰지 않음 | 다른 단위의 보호 규칙도 삭제하지 않음. 이 단위 제외는 그 보호가 불필요하다는 판단이 아님 |

원본 `features.token_budget_warn_at`는 `effects-map.md:2208`에서 **아직 실제 reader 없음**이라고 명시한다. `statusline.sh:59–64`는 받은 context percentage를 표시하지만 이 설정을 읽지 않는다. 따라서 최소 복원에서 삭제해야 할 실동작 토큰 경고 호출이 있다고 오인하지 않는다. `statusLine` 자체를 채택하지 않고, 토큰 경고 키·그 설정 UI·사용량 파일을 새 의존성으로 넣지 않는 것이 구체적인 제외안이다. 에이전트 spawn 수 역시 금액/실제 token 사용량 측정이 아니다.

### 7.1 이벤트 설정 병합안

첫 단계 추천은 `hooks.SessionStart`의 단일 command만 등록하는 것이다. 원본 `settings.json:185–186`의 Bash/PowerShell 양쪽 파싱 형태와 프로젝트 경로 quoting은 보존하되, 무 Git Bash 경고를 **'자동 세션 복원을 실행하지 못함; active.md를 수동으로 읽을 것'**으로 바꾼다. 최소 구성에 없는 read/write/commit 검사가 꺼졌다는 원본 문구를 그대로 쓰지 않는다.

| 키 | 추천 병합 내용 |
|---|---|
| `hooks.SessionStart` | 축소한 대상 `.claude/hooks/session-start.sh` 한 개. matcher 빈 문자열로 원본의 시작/재개/clear/compact 범위를 유지하되 대상 실행기에서 실제 발화 확인 |
| timeout | 원본 20초는 외부 안전망으로 우선 보존 가능. 정상 복원은 대상 장비에서 2초 안팎 이하를 목표로 측정. 여기서 측정한 결과는 아님 |
| `hooks.Stop` | 기본 없음. 선택하면 축소·쓰기 성공/동시성 보완 뒤 등록 |
| `hooks.PreCompact` | 기본 없음. 진단 필요 시 원본처럼 시각 로그만 기록하는 기능으로 명시 |
| 그 밖의 이벤트 | 이번 단위가 추가하지 않음. 다른 단위가 승인한 기존 이벤트는 유지 |
| 권한 | 읽기 helper는 필요한 호출만, checkpoint/append/rotate는 승인한 작업 스킬의 좁은 범위로 다룸. `Bash(*)`나 원본 전체 allow 목록을 복사하지 않음 |

`session_state_enabled()`는 source하여 쓰는 함수이며 yaml-helper의 직접 실행 dispatcher(`:2316–2352`)에 이 함수 이름은 없다. `bash yaml-helper.sh session_state_enabled`를 설정 command로 등록하면 실제 검사가 아니라 usage 출력/종료가 된다. **실행형 이벤트 entry point는 session-start.sh, 설정해석 라이브러리는 yaml-helper.sh**로 구분한다.

### 7.2 대상용 체크포인트 내용 예시

다음은 **문서 안의 형식 예시**이며 active.md를 생성한 것이 아니다. 25줄 안에서 가장 중요한 재개 정보부터 놓는다.

```markdown
<!-- STATUS -->
Epic:
Feature:
Task:
<!-- /STATUS -->
<!-- CHECKPOINT -->
**Updated:** 2026-11-11T00:00:00+09:00 [예시 시각, 실제 저장 시 교체]
**Branch:** `[실제 git branch]`
**Current task:** TrackBuilding — [승인된 작업, 진행/INCOMPLETE/BLOCKED]
**Next step:** [첫 미완료 행동, 필요한 파일/명령]
**Blocked on:** [없음 또는 구체적 미해결 입력]
**Files in progress:** [Source/TrackBuilding/... 또는 문서 경로]
**Run result:** NOT VERIFIED — [실행 여부·이유; 실행했다면 증거 경로]
**Verification:** Build: 미실행 / Automation: 미실행 / Host: 미검증 / Client: 미검증
**Open questions:** [결정되지 않은 항목 또는 없음]
<!-- /CHECKPOINT -->
```

STATUS는 원본 statusline에서 Production/Polish/Release 때 쓰는 선택 breadcrumb다. Concept에서 억지 Epic/Feature/Task를 만들지 않고 공란으로 보존한다. 상태 본문에 실제 프로젝트명·검증 경계를 쓰는 것으로 충분하며 자동으로 게임 단계나 설정값을 변경하지 않는다. `Next step`은 읽고 판단하는 데이터이고 shell `eval` 대상으로 삼지 않는다.

## 8. 누락·상한·브랜치·로그와 Windows/Git 운영

### 8.1 현재 구현의 복구 한계

| 사례 | 원본의 실제 처리와 한계 | 대상 도입 시 기준 |
|---|---|---|
| active.md 없음 | SessionStart 상태 블록은 출력하지 않음. show는 exit 4. checkpoint/append는 템플릿으로 생성 | 상태 없음과 훅 미발화를 구분. 새 상태를 추정하지 말고 현재 승인 작업부터 작성 |
| 템플릿 없음 | 기존 정상 상태는 읽을 수 있어도 새 상태 생성은 실패(exit 1) | 새 checkout 생성 경로까지 템플릿 동반 확인 |
| feature off | 다섯 원본 훅만 영향을 받음. 시작 훅은 복원 블록만 off. 스킬/helper의 상태 쓰기·statusline은 계속 가능 | `off`가 상태 파일 삭제나 쓰기 차단이 아님을 안내 (`effects-map.md:2007–2020,2056–2074`) |
| 두 marker 모두 없음/본문 비어 있음 | 시작은 첫20줄 fallback; checkpoint helper는 STATUS 뒤 또는 앞에 블록 삽입. show는 실패 | 원본 fallback은 문자 무제한이라 수정. legacy 자료를 자동으로 최신 완료 상태로 삼지 않음 |
| opener만 있음 | 시작은 경고하고 preview하지 않음. checkpoint/show는 거부. rotate는 closer 없으므로 거부 | 거부와 수동 복구 안내 유지 |
| closer만 있음/역순/중복 | 시작·writer·rotate의 판정이 같지 않음. rotate는 opening 없이도 closing 기준 절단 가능 | §7의 동일한 parser/계약으로 통일 후 도입 |
| 긴 checkpoint | 시작은 최대25줄/awk 길이 합3000 이내 출력, 초과 시 잘림 안내. writer는 줄 수 경고만 | 핵심 필드를 앞에 놓고 모든 preview 분기에 총량 상한 적용. 문자/byte/token 차이를 구분 |
| 긴 Notes | 시작은 총 파일 줄 수 200 초과에 회전 안내만. rotate는 narrative 5줄 초과면 동작 가능 | 200은 자동 실행 임계값도 hard cap도 아님. 생성 안내만의 재회전 방지. 세션 로그 자체에는 원본 용량/보존 상한 없음 |
| 브랜치 변경 | CHECKPOINT Branch의 첫 backtick 값/첫 단어를 현재 브랜치와 비교, 경고만. 빈값·placeholder·detached HEAD/비저장소는 비교 생략 | 경고 후 관련 파일·현재 diff 확인. 다른 브랜치 상태를 자동 실행하지 않음. 같은 브랜치에서 과거 commit으로 이동한 경우도 식별할 HEAD 기록 추천 |
| 갑작스런 중단 | 마지막 **작성된** checkpoint까지만 복구. Stop/PreCompact가 대화의 미기록 결정을 새로 만들지 않음 | 작업 중간에도 저장. 미완료 위치·실패·미실행을 남김. 상태와 실제 부분 파일을 함께 확인 |
| Stop 로그 쓰기 실패 | stderr 억제·항상 exit0. hash 갱신이 성공하면 실패한 본문이 다음에 생략될 수 있음 | 성공 확인 후 hash commit, 진단 가능성 확보. active.md를 지우지 않음 |
| 회전 중 실패 | archive 작성/비교/rename 실패 전에는 active 보존. 그 뒤 active 교체 실패이면 이중 사본 가능 (`:85–159`) | 오류를 성공으로 보고하지 않음. 재실행 전 두 사본 확인; 새 변경안은 generation으로 중복 판별 |
| 동시 세션 | 원본 lock 없음. active 교체/append/회전/async Stop이 겹칠 수 있음 | 같은 worktree에 주 작성자 하나. 잠금·snapshot 보완 전 여러 세션이 한 active를 쓰게 하지 않음 |

원본 종료 로그의 `git log --since="8 hours ago"`는 세션 ID로 작업 경계를 추적한 결과가 아니며, `git diff --name-only`는 staged-only·untracked·동일 파일 내용 변화까지 완전하게 요약하지 않는다. 이 로그를 검증 기록이나 완전한 변경 목록으로 사용하지 않는다. 최소안은 해당 관찰을 제거한다.

### 8.2 Windows 실행 환경

- 원본 실행 환경 계약은 `setup-requirements.md:48–59` 및 `.gitattributes:1–14`에 있다. shell/awk는 **LF**, UTF-8 한글 상태 본문은 깨지지 않도록 유지한다. 대상에는 현재 `.gitattributes`가 없으므로 도입 시 shell/awk 규칙만 병합하는 안을 추천한다. 원본 전체 Git 설정 복사는 불필요하다.
- 이번 PowerShell `Get-Command` 조회에서 `bash`는 `C:/WINDOWS/system32/bash.exe`로 잡혔고, 실제 `C:/Program Files/Git/bin/bash.exe`, `C:/Program Files/Git/usr/bin/awk.exe` 파일은 존재했다. `git.exe`는 Git for Windows 경로, Python은 Python312 경로에 있었다. **존재/PATH 조회만 했으며 훅 실행 성공 검증은 아니다.** bare bash를 Git Bash로 가정하지 말고 대상 Claude 실행 환경이 Git Bash를 선택하는지 확인해야 한다.
- 원본은 필요 시 `CLAUDE_CODE_GIT_BASH_PATH`로 Git Bash 경로를 지정하는 방법을 문서화한다. 이 보고서가 설정하거나 설치하지 않았다. PowerShell fallback command는 복원 미실행을 알리고 수동 경로를 제시해야 한다.
- `session-state.sh:132–142`는 Git Bash gawk의 CRLF text-mode 차이를 고려해 `BINMODE=3`을 사용하고 기존 상태의 줄바꿈을 보존한다. 기존 보호를 제거하지 않는다. LF shell과 CRLF 상태 파일은 별도 문제다.
- 최소 복원 핵심은 Bash/awk로 동작하며 Python·jq를 요구하지 않도록 축소할 수 있다. 공용 설정의 일반 `get_yaml_key`/`resolve_config`·enum 검증을 사용하면 Python 3이 필요하다. PyYAML은 필요 없다. Python 부재가 기본값 fallback으로 이어졌는데 실제 설정이 적용됐다고 보고하면 안 된다.
- 엔진 빌드/테스트/실행은 복원 훅에서 호출하지 않는다. 대상 `project.yaml:41–44`의 PowerShell 명령은 향후 승인된 게임 작업이 소비한다. `commands.run` 단독 실행은 호스트·클라이언트 2인 검증의 성공 증거가 아니다.

### 8.3 상태·이력의 Git 공유 방식

원본 `.gitignore:13–18`은 local 설정과 `production/session-logs/`, `production/session-state/`를 모두 제외한다. `.worktreeinclude:1–8`은 local 설정 세 개만 복사 대상으로 두고 active.md·로그는 포함하지 않는다. 따라서 원본 방식은 **한 worktree의 로컬 연속성**이며 새 clone·다른 PC·다른 worktree로 자동 이어지는 공유 메모리가 아니다.

추천은 원본처럼 **상태·해시·런타임 로그는 로컬**, helper·템플릿·짧은 복구 문서·공유 이벤트 설정은 Git 추적하는 방식이다. 현재 대상 `.gitignore:1–75`에는 이 세션 경로 제외 규칙이 없으므로, 실제 이관 때 아래 선택 규칙을 기존 Unreal ignore에 추가해야 한다. 이 보고서에서는 추가하지 않았다.

```gitignore
/production/session-state/
/production/session-logs/
/project.local.yaml
/.claude/settings.local.json
```

Notes·상태에는 작업 경로·결정·실패 이유·개인 환경이 담길 수 있다. 다른 PC로 인계가 필요하면 검토한 현재 작업 요약만 `Doc/` 아래의 합의된 인계 문서로 명시 저장하는 방식을 조건부로 추천한다. 원시 로그·해시·active.md를 자동 커밋/푸시하거나 원본 `.worktreeinclude`에 통째로 추가하지 않는다. Git 추적 방식으로 active를 공유하기로 별도 결정한다면 브랜치 충돌·동시 작성·민감 정보·상태 최신성 기준을 먼저 정하고 로컬 기본안과 섞지 않는다.

## 9. 이관 순서·완료 확인 기준·미확정 사항

### 9.1 향후 이관 순서

1. **실행기와 상태 공유 방식을 결정한다.** Claude Code에서 실제 hook 이벤트를 사용할지 확인하고, 로컬 active/로그 기본안을 채택할지 정한다. 이는 본 보고서의 추천이며 확정 결정이 아니다.
2. **공용 설정 라이브러리 담당과 인터페이스를 맞춘다.** yaml-helper를 여러 이관 단위가 중복·서로 다른 버전으로 덮어쓰지 않도록 하나로 유지한다. 현재 project.yaml·파생값·기술 문서 보존을 확인한다.
3. **템플릿·writer·회전 helper와 짧은 운영 설명을 준비한다.** marker·한글·중단·동시성·원자 쓰기 변경안을 먼저 반영한다. 프로젝트 이름은 TrackBuilding, 대상 루트만 TrackBuilding2다.
4. **작업 소비자에 작성 책임을 연결한다.** 승인된 작업의 중간 결과·실패/미실행·다음 행동을 checkpoint로 교체하는 계약을 둔다. 전체 sprint·team 역할·Story 파이프라인을 선행 설치하지 않는다.
5. **LF/ignore·축소 SessionStart·선택 설정을 병합한다.** 전체 settings·전체 project.yaml 복사 없이, 실행할 파일이 모두 준비된 후 마지막에 이벤트를 켠다.
6. **아래 복원 사례를 격리된 임시 작업 트리에서 검증한다.** 게임 엔진 실행과 별개로 기록한다. 그 뒤 이력이 필요한 경우에만 축소 Stop, 진단이 필요한 경우에만 PreCompact를 추가한다.

### 9.2 완료로 판정하려면 필요한 관찰

이 목록은 **향후 이관 검증 기준**이며 이번에 실행한 테스트가 아니다.

| 구역 | 확인 사례 / 통과 기준 |
|---|---|
| 설치 경계 | 선택 파일만 존재하고 참조가 모두 닫혀 있음. update-check/bug-stats/sprint/agent-cost/token 관찰·엔진 실행 호출 없음. settings의 다른 단위 설정 유지 |
| 설정 불변 | stage=Concept·goal=hobby·rigor=minimal·automation=collaborative 및 여섯 파생값 유지. 기존 CLAUDE/project.yaml/기획/프레임워크 문서 내용 보존 |
| 작성 | 파일 없음+템플릿 존재 시 생성, checkpoint 교체 시 Notes 보존, 동일 append 말미 중복 방지, show 정상 출력·부재 exit4. stdin 없음/빈값·템플릿 없음·쓰기 불가에서 거짓 성공 없음 |
| 구조 | LF/CRLF/한글, marker 앞뒤 공백, 문장 속 marker 인용, opener/closer 각각 누락·역순·중복·빈 블록. 읽기·쓰기·회전의 판정 일치 |
| 시작 이벤트 | 실제 새 세션·resume·clear·compact 각각에서 checkpoint가 모델 컨텍스트에 도달. 파일 없음을 hook 미발화와 구분. 필요하면 무시되는 진단 로그에 FIRE/DONE 쌍을 잠시 사용 |
| 루트 | 루트·Source/TrackBuilding 하위·.claude 하위·공백 포함 경로·환경 변수 부재·잘못된 환경·중첩 프로젝트·cd 실패. 원본이나 엉뚱한 production 폴더에 쓰지 않음 |
| 브랜치/최신성 | 일치·불일치·placeholder·detached HEAD·Git 실패·같은 브랜치 HEAD 변경. 경고/미검사 사실을 구분하고 자동 작업 실행 없음 |
| 상한 | 25줄/3000 단위 경계·한 줄만 매우 긴 상태·큰 Notes·무 marker fallback에서도 출력 상한과 처리 시간 유지. 정상 목표 약2초는 실제 장비에서 측정 |
| feature | 미설정/기본 on, base/local on/off, false/오타/잘못된 YAML 표기·orphan local. off에서 파일 쓰기와 삭제 정책을 혼동하지 않음 |
| 회전 | dry-run byte 무변경. 정상은 checkpoint 보존+Notes byte 일치. 로그 경로가 디렉터리/파일로 충돌·쓰기 불가·중간 종료·active 교체 실패 때 유실/거짓 성공 없음. 재실행이 생성 안내만 중복 보관하지 않음 |
| 선택 Stop | 같은 bytes 반복 시 1회, changed bytes 추가, hash 실패 시 유실 없이 중복 허용, 보관 실패 때 hash 미진행. 동시 writer/회전·반복 Stop·headless teardown을 별도 관찰. active.md 삭제 없음 |
| Git/공유 | runtime 경로 `git check-ignore` 확인, helper/template/settings는 의도대로 추적. 새 clone·worktree에 로컬 상태가 없는 경우 수동 인계 절차가 명확 |
| 실제 재개 | INCOMPLETE/BLOCKED/NOT VERIFIED 상태에서 이전 작업·다음 행동·부분 파일·미검증 결과를 정확히 읽음. 빌드/Automation/호스트/클라이언트 성공을 만들어내지 않음 |

### 9.3 남겨 둔 결정과 이번 완료 범위

- 미확정: 실제 훅 소비 실행기/버전, 로컬 상태 유지 vs 검토한 Git 인계 문서 공유, Stop 이력·PreCompact 진단의 채택 여부, 상태 잠금·원자 교체 시 링크 지원 정책, 로그 보존 기간/용량.
- 미확정: 공용 yaml-helper 전체 API와 엔진 표를 다른 단위에서 어디까지 채택할지. 최소 복원만으로 전체 설정·엔진 기능 설치가 끝났다고 보지 않는다.
- 확인 필요: 대상의 지정된 `technical-preferences.md`가 현재 없는 이유와 필요 여부. 복원은 현재 project.yaml을 기준으로 설계할 수 있으므로 이를 원본으로 덮어 채울 이유는 없다.
- 이번에 완료한 것은 원본/대상 읽기, 18개 훅·54개 스크립트 및 settings 이벤트의 정적 분류, 의존성/실패 경계·대상용 변경안·검증 기준 정리와 **이 보고서 하나의 저장**이다. 실제 스킬·역할·규칙·훅·설정·상태 파일 이관, 원본 수정/삭제, 엔진 실행·빌드·테스트·커밋은 수행하지 않았다.

주요 원본 바로가기: [시작 복원](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/hooks/session-start.sh:279), [상태 작성 도우미](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/scripts/session-state.sh:14), [회전](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/scripts/rotate-session-state.sh:47), [종료 보관](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/hooks/session-stop.sh:58), [설정 라이브러리](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/hooks/yaml-helper.sh:793), [이벤트 등록](C:/Users/Sangha_Remote/Documents/Claude-Code-Game-Studios/.claude/settings.json:179).
