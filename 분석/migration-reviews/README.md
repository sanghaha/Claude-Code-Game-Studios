# CCGS → TrackBuilding2 이관 단위별 검토

작성일: 2026-11-11 (Asia/Seoul). 원본 CCGS v1.3.0, 커밋 `abc0de79d6ceb0b5d7aa693945849b3937afbe6a`.

요청한 6개 단위를 각각 별도 채팅에서 분석하고 보고서로 저장했다. 프로젝트 공통 지침은 완료된 범위이므로 제외했다. 이 문서는 추천·제외·Unreal용 변경사항을 비교하기 위한 색인이다. 추천은 아직 채택 확정이 아니며, 제외는 원본 삭제가 아니라 해당 이관 묶음에서 뺀다는 뜻이다.

## 1. 단위별 결과

| 이관 단위 | 조사한 범위 | 지금의 추천 | 현재 제외·조건부 | 상세 보고서 |
|---|---|---|---|---|
| 파일별 규칙 | rules 전체 14파일 | 설계·게임플레이·네트워크·테스트 기준·Unreal 자산 편집 경계의 5파일을 수정 이관 | 조건부 6, 현재 제외 3. AI·서사·프로토타입 규칙은 현재 제외 | [규칙 보고서](C:/Users/Sangha_Remote/Documents/TrackBuilding2/ax-setup/migration-reviews/01-file-rules.md) |
| 기획·설계 절차 | 14개 스킬 폴더, 65파일 | 기존 기획에서 작업용 brief를 얻는 어댑터. brainstorm의 신규 아이디어 질문을 기존 기획 입력으로 변경 | 상세 GDD·ADR은 위험 기능에 선택 사용. 전체 manifest·다중 GDD 리뷰·역문서화·UX 전체 경로는 필요 조건이 생긴 뒤 | [기획·설계 보고서](C:/Users/Sangha_Remote/Documents/TrackBuilding2/ax-setup/migration-reviews/02-planning-design.md) |
| 구현 작업 절차 | 22개 스킬 폴더, 61파일 | create-stories → dev-story → story-done의 핵심 7파일과 공용 helper·형식·역할 | 전체 Epic/스프린트/단계 게이트/QA 팀·출시 hotfix는 최소 경로에서 제외. readiness·코드 검토·스모크는 필요 시 추가 | [구현 절차 보고서](C:/Users/Sangha_Remote/Documents/TrackBuilding2/ax-setup/migration-reviews/03-implementation-workflow.md) |
| 산출물 형식 | templates 전체 74파일 | game-brief, story, session-state, test-evidence의 4형식. 현재 검토는 유지 1·수정 3·조건부 55·제외 15 | 타 엔진·웹 코드 서식과 미사용 플랫폼·장르·출시 형식은 현재 제외 또는 조건부 | [형식 보고서](C:/Users/Sangha_Remote/Documents/TrackBuilding2/ax-setup/migration-reviews/04-artifact-templates.md) |
| Unreal 전문 역할 | agents 전체 49파일 | engine/gameplay/network/UI programmer + unreal-specialist + ue-replication-specialist의 6역할 후보 | 조건부 26·현재 제외 17. 다른 엔진 역할, 미사용 GAS·서사·운영 역할 등 | [역할 보고서](C:/Users/Sangha_Remote/Documents/TrackBuilding2/ax-setup/migration-reviews/05-unreal-roles.md) |
| 자동 세션 복원 | hooks 18 + scripts 54파일 및 이벤트 설정 | 축소 session-start, yaml-helper, session-state, rotate-session-state, 상태 템플릿의 5파일과 필요한 이벤트 설정 조각 | Stop 보관·PreCompact 로그는 선택. 업데이트·전체 스프린트/버그/비용 관찰 등은 이 복원 묶음에서 제외 | [복원 보고서](C:/Users/Sangha_Remote/Documents/TrackBuilding2/ax-setup/migration-reviews/06-session-recovery.md) |

각 보고서는 파일별 판정, 동반 의존성, 원본 위치·문제·변경안·소비자 영향, 이관 순서, 완료 확인 기준, 미확정 사항을 포함한다. 폴더 전체를 통째로 가져오라는 목록이 아니다. 최소 스킬에서도 일부 references/는 필요하고 다른 references/는 제외할 수 있으므로 해당 파일별 표를 따른다.

## 2. 유지할 프로젝트 전제

- `project.stage: Concept`, `project.goal: hobby`, `modes.rigor: minimal`, `modes.automation: collaborative`를 유지한다. 여섯 파생 설정은 YAML에 별도로 쓰지 않는다.
- 작업 폴더는 TrackBuilding2지만 Unreal 프로젝트·타깃·모듈명은 TrackBuilding이다. Source/TrackBuilding과 기존 project.yaml의 Windows/PowerShell 명령을 기준으로 한다.
- 기존 Doc/기획.md를 게임 의도의 기준으로 유지한다. brief와 작업 문서는 원기획의 범위·절을 연결하는 파생 자료다.
- 완료된 공통 지침·엔진 설정·unreal-framework-map을 원본 CCGS 문서로 덮어쓰지 않는다. 현재 없는 technical-preferences.md를 legacy 호환성만을 위해 다시 만들지 않는다.
- minimal에서도 UI/Visual 증거·실행 여부·호스트와 클라이언트의 동작 확인은 따로 기록한다. 테스트 면제·테스트 0개·미실행은 PASS의 근거가 아니다.

## 3. 단위 사이에서 함께 맞춰야 할 부분

| 연결 | 함께 정할 내용 | 관련 보고서 |
|---|---|---|
| 원기획 → brief → Story | brief 위치, Features/Build order, 원기획 절 연결. 기존 게임의 아이디어를 다시 고르거나 기획을 요약본으로 덮어쓰지 않음 | 02·03·04 |
| 문서 경로 | Doc/GDD·ADR·Story·증거·상태의 경로를 정한 뒤 작성 스킬, 읽는 스킬, catalog, 검사 스크립트, rules glob을 한 묶음으로 수정 | 전체 |
| 기계가 읽는 형식 | Story 필드·상태·ID, GDD 절 제목과 표, ADR 상태, CHECKPOINT/STATUS marker. 이름을 바꾸려면 모든 소비자도 함께 변경 | 02·03·04·06 |
| 규칙 적용 | 네트워크 폴더명만으로 한정하면 기능별 폴더의 RPC·복제 코드를 놓침. Source/TrackBuilding 전체와 실제 문서 경로를 다루고 writer/리뷰어가 필요한 규칙을 명시적으로 읽게 연결 | 01·03·05 |
| QA 요구의 충돌 | gameplay 규칙의 모든 로직 단위 테스트 요구와 minimal의 면제를 맞춤. 최신 Editor 빌드·Automation·NullRHI 스모크·2인 플레이 결과를 구분 | 01·03·04 |
| 리슨 서버 정책 | 호스트 종료 시 게임 종료. host migration을 필수로 이관하지 않음. 서버 판정, 소유 RPC 경로, 호스트·원격 클라이언트 표시 경로를 구분 | 01·04·05 |
| 역할의 실제 호출 | individual/solo는 모든 agent 금지가 아님. dev-story의 프로그래머·엔진 검토는 남음. additional에 이름을 적는 것만으로 복제 전문가가 호출되지는 않으므로 호출자를 연결 | 03·05 |
| Unreal 기술 전제 | GAS·CommonUI·Iris·예측/롤백을 일괄 도입하지 않음. 사용 API와 플러그인 상태는 설치된 5.8.4 근거로 확인 | 01·05 |
| 상태 작성 → 복원 | 작성 책임·갱신 시점이 먼저 있어야 함. PreCompact는 요약 저장이 아님. marker 검사·원자적 저장·회전·보관 실패 시 해시 처리 등을 일관되게 수정 | 03·04·06 |
| 공용 파일의 소유 | yaml-helper, md-section, Story/상태 템플릿 등은 여러 묶음이 참조하더라도 사본 하나를 유지. settings.json은 필요한 이벤트·권한 조각만 병합 | 전체 |

## 4. 실제 이관 순서 제안

1. 최신 대상 파일과 이 보고서의 판정을 대조하고 brief·GDD·ADR·Story·증거·체크포인트 경로를 확정한다. 이미 추가된 파일을 새 원본으로 덮어쓰지 않는다.
2. 공용 설정 해석, 산출물 형식, Unreal 규칙의 소비 계약을 맞춘다. 기본 설정값과 공통 지침을 바꾸는 단계가 아니다.
3. 기존 기획 입력 어댑터, 핵심 Story 생성·구현·종료, 필요한 프로그래머·Unreal 검토 역할을 함께 이관한다.
4. 상태 작성 도우미와 축소 시작 복원을 연결한다. 정상 상태뿐 아니라 파일 없음·잘못된 marker·다른 브랜치·중단·보관 실패를 확인한다.
5. 실제 기능을 진행하며 필요한 상세 기획·ADR·독립 리뷰·QA 기능을 조건부로 추가한다. 전체 조직·게이트를 먼저 설치하지 않는다.

이관 완료 판정은 각 묶음의 실제 입력·출력·실행 증거로 한다. 이 보고서 작성과 파일 존재·링크 검증은 게임 구현·빌드·플레이 검증을 대신하지 않는다.

## 5. 작성 시점과 현재 대상 상태

보고서들은 조사 당시 대상 상태를 기록한다. 작성 초기에 없던 일부 `.claude/skills/`, 공용 문서·helper, `Doc/GDD/systems-index.md`, `production/session-state/active.md`가 통합 점검 중 추가로 관찰됐다. 보고서 안의 '현재 없음/미설치'는 그 보고서 조사 시점의 관찰이며 이후 설치 완료 여부를 뜻하지 않는다.

이 검토에서는 기존 파일을 원복하거나 새로 관찰된 파일을 삭제·덮어쓰지 않았다. 실제 이관을 시작할 때는 최신 대상의 내용·경로·의존성과 다시 대조한다. 특히 이미 존재하는 Doc/GDD와 체크포인트 경로를 먼저 확인하고 별도 경로를 중복 생성하지 않는다.

## 6. 검증과 세션 기록

- 파일명 색인 점검: rules 14, 기획·설계 65, 구현 절차 61, templates 74, agents 49, hooks 18, scripts 54의 총 335개 관할 파일이 각 보고서에서 언급됨을 확인했다. 같은 basename이 반복되는 파일은 해당 폴더 판정표와 함께 해석한다.
- 실제 파일·행으로 연결된 Markdown 링크 164개에 대해 대상 존재와 행 번호 범위를 확인했다. 누락·범위 초과는 발견되지 않았다. 일반 텍스트로 적힌 모든 인용의 의미까지 자동 검증했다는 뜻은 아니다.
- 보고서 6개는 UTF-8로 읽히며 대체 문자와 일본어 문장 혼입이 없다.
- 시작 시 기록한 기존 대상 파일 17개의 해시는 통합 점검 시 유지됐다. 추가로 관찰된 파일은 별도로 기록했다.
- 파일별 규칙 채팅은 정상 완료했다. 나머지 5개 채팅은 보고서 저장 후 마지막 점검·최종 응답 단계에서 서비스 사용량 제한으로 종료됐다. 생성된 보고서를 이 대화에서 읽고 목록·링크·공통 경계를 최종 점검했다.

채팅 ID와 원래 종료 상태는 [세션 목록](C:/Users/Sangha_Remote/Documents/TrackBuilding2/ax-setup/migration-reviews/sessions.json), 검증 집계는 [검증 기록](C:/Users/Sangha_Remote/Documents/TrackBuilding2/ax-setup/migration-reviews/review-validation.json)에 있다. 세션의 실패 상태를 성공으로 바꾸어 기록하지 않았다.
