# Claude TC 자동화 상세 런북

Updated On: 2026-08-20

Audience: Claude Code 또는 동등한 외부 reviewer/authoring agent

Scope: 릴리즈 근거를 구조화된 TC 제안으로 만들고, 기존 자산과 실행 결과를 손상하지 않도록 검증하는 공개용 절차

## 1. 역할과 권위

Claude는 다음 역할 중 하나만 명시적으로 맡는다.

1. **Evidence reviewer:** 문서, 코드·심볼, 스크린샷과 기존 TC에서 확인된 사실과 누락 질문을 정리한다.
2. **TC proposal author:** 승인된 근거만 사용해 신규·수정·retire 제안을 만든다.
3. **Read-only code reviewer:** base/head 차이와 검증 증거를 검토해 finding을 제시한다.

Claude의 출력은 최종 권위가 아니다. 권위 순서는 다음과 같다.

1. 사용자가 확인한 제품·릴리즈 결정과 실제 앱 관찰
2. 활성 Current Phase 문서와 제품별 릴리즈 근거
3. Architecture와 Best Practices
4. 정책 YAML, schema, validator
5. 제품별 YAML TC와 run 기록
6. 생성된 Markdown, 웹, Excel
7. Claude 제안과 review finding

## 2. 시작 전 Scope Lock

작업을 시작하기 전에 다음 값을 한 번에 기록한다. 하나라도 불명확하면 mutation을 중단하고 질문한다.

```yaml
task_id: <TASK_ID>
product_id: <PRODUCT_ID>
repository: <REPOSITORY>
branch: <PRODUCT_BRANCH>
release_id: <RELEASE_ID>
jira_project: <JIRA_PROJECT_OR_NONE>
jira_fix_version: <EXACT_FIX_VERSION_OR_NONE>
patch_identity: <PATCH_DATE_OR_BUILD>
target_run_id: <RUN_ID_OR_NEW>
source_declaration:
  release_note: <APPROVED_SOURCE>
  screenshots: <APPROVED_SOURCE_OR_NONE>
  package_root: <APPROVED_SOURCE_OR_NONE>
output_scope:
  yaml: true
  web: true
  excel: false
```

### Scope Lock 규칙

- 제품 UI가 비슷하다는 이유로 다른 제품의 기능, TC, dataset 또는 장비 전제를 가져오지 않는다.
- 새 릴리즈가 선언되면 과거 릴리즈의 파일 경로, 패키지 날짜와 이미지 위치를 승계하지 않는다.
- Fix Version이 바뀌면 새 release 후보로, 같은 Fix Version에서 patch identity가 바뀌면 새 run 후보로 본다.
- 제품별 자산은 해당 제품 branch와 data root에서만 수정한다.
- 공통 runtime 변경과 제품별 TC 변경을 한 commit에 섞지 않는다.

## 3. 필수 읽기 순서

저장소가 ActiveDocs 체계를 사용한다면 아래 순서로 읽는다.

1. `AGENTS.md` 또는 저장소 agent guide
2. `CLAUDE.md`
3. `docs/ActiveDocs.md`
4. 현재 작업에 필요한 Current Phase 문서
5. 관련 Architecture와 Best Practices
6. TC authoring skill과 policy YAML
7. 제품 release, change, precondition, dataset, suite, TC, run
8. 이번 작업의 release note, screenshots, code/symbol evidence

읽지 않은 문서를 읽었다고 보고하지 않는다. 경로가 없으면 없는 상태를 기록하고 다음 권위로 진행한다.

## 4. Evidence Gate

모든 요구사항을 원자 주장으로 나누고 다음 중 하나로 분류한다.

| 분류 | 의미 | TC 반영 |
|---|---|---|
| `confirmed` | 승인된 문서, 실제 UI, 코드·심볼 또는 사용자 확인으로 검증됨 | 절차와 기대 결과에 사용 가능 |
| `inference` | 여러 증거로 합리적이지만 직접 확인되지 않음 | 질문 또는 탐색 후보로만 사용 |
| `recommendation` | 더 안전하거나 일관된 동작에 대한 제안 | 제품 결정 전에는 기대 결과로 사용 금지 |
| `unresolved` | 근거 충돌, 누락 또는 제품 경계 불명 | mutation 차단, 사용자 질문 |

Evidence table 예시:

```yaml
- claim_id: C-001
  claim: "현재 화면에 정렬된 행 순서가 export 파일에도 유지된다."
  classification: confirmed
  source: "approved release note and observed UI export"
  affected_states:
    - queried
    - sorted
    - exported
```

### 질문 Gate

다음 중 하나가 빠지면 TC 작성 전에 질문한다.

- 기능 trigger와 진입 화면
- 대상 사용자 또는 제품 variant
- 기본값과 persistence 범위
- 정상 완료와 오류 처리
- 데이터 경계, 기간, 건수 또는 모델 수
- reload, cancel, close/reopen 뒤 기대 상태
- export가 화면 snapshot인지 source requery인지
- 기존 Jira 또는 known issue와의 관계

질문은 한 번에 관련 항목을 묶어 보내고, 답을 추측해 TC를 만들지 않는다.

## 5. 요구사항 분해

각 기능을 다음 순서로 분해한다.

```text
기능
-> 사용자가 관찰하는 상태
-> 상태 전이와 trigger
-> 정상 경로
-> 예외 경로
-> 경계값
-> 이전 상태 잔존 위험
-> 기존 TC 중복
-> 영향 모듈
-> 신규/수정/retire 제안
```

최소 산출물:

```yaml
feature: <FEATURE>
states: [closed, opened, loading, loaded, empty, error]
transitions:
  - from: loaded
    action: change_search_condition
    to: loading
normal_cases: []
exception_cases: []
boundary_cases: []
stale_data_risks: []
existing_tc_matches: []
affected_modules: []
proposal: create|update|retire|reuse
```

## 6. TC 작성 규칙

### 기본 계약

- 제목은 짧고 UI 경로 기호를 넣지 않는다.
- 사전 조건은 절차와 분리한다.
- 진행 스텝은 1~7개다.
- 한 스텝은 한 개의 관찰 가능한 조작 또는 확인으로 끝낸다.
- 기대 결과는 화면 상태, 데이터 정합성, 이전 데이터 비혼입과 오류 부재를 관찰 가능한 문장으로 쓴다.
- `같은 방법`, `필요하면`, `적절히`, `정상적으로` 같은 추론 요구 표현을 사용하지 않는다.
- reusable TC 정의에 tester, run status, Jira comment를 넣지 않는다.

### 초보자 실행 가능성

절차만 읽은 신규 QA가 다음을 추론하지 않아도 되어야 한다.

- 어느 창과 탭을 열어야 하는가
- 어떤 검색 조건을 입력해야 하는가
- 버튼을 언제 클릭하는가
- 어떤 값과 화면을 비교하는가
- 다음 TC 전에 상태를 초기화해야 하는가

### UI 용어

프로젝트 표준이 있으면 그 용어를 우선한다. 일반 기준은 다음과 같다.

- `"창"`, `[탭]`, `'옵션 그룹'`
- `[버튼명] 버튼`, `텍스트 상자`, `체크 박스`, `라디오 버튼`
- `콤보 박스`, `리스트 박스`, `툴팁`, `스핀 버튼`
- `메뉴 확장 버튼`, `컨텍스트 메뉴`
- 창, 목록, 메뉴와 탭은 `출력`으로 표현

## 7. WPF 상태 잔존 필수 매트릭스

조회형 WPF 화면에는 최소한 다음 변형을 검토한다.

| 전이 | 확인할 위험 |
|---|---|
| 기본 open | 이전 창 인스턴스의 selection, filter, collection 잔존 |
| 단일 모델 query | 선택 모델 이외 데이터 혼입 |
| 같은 창에서 다른 모델 query | 첫 모델 행, 합계, 이미지, 상세 패널 잔존 |
| 2개 모델 query | 모델별 집계와 전체 집계 중복 또는 누락 |
| 3개 이상 모델 query | ordering, virtualization, aggregation 경계 |
| 기간 A 후 기간 B | 과거 기간의 행과 chart series 잔존 |
| barcode A 후 barcode B | 상세 데이터와 selection 잔존 |
| 결과 있음 후 0건 | 이전 행, 합계, 이미지가 남는 오류 |
| 성공 후 오류 후 성공 | error overlay와 old collection 잔존 |
| 요청 A 후 요청 B | 늦게 끝난 A가 B를 덮는 async completion reversal |
| loading 중 cancel | 일부 데이터, spinner, command state 잔존 |
| 반복 refresh | event 중복, 메모리 증가, 중복 행 |
| close/reopen | static cache 또는 singleton state 잔존 |

단일 모델도 최소 open, query, changed-condition reload의 세 TC로 분리한다.

## 8. Export 계약

먼저 export source를 확인한다.

```text
A. 현재 화면의 정렬·필터·선택 상태 snapshot
B. export 시점의 source 재조회
C. 현재까지 누적 조회한 batch snapshot
```

확인되지 않은 방식을 추정하지 않는다. 화면 snapshot 계약이라면 다음을 검증한다.

- 화면 행 수와 파일 행 수
- 화면 정렬 순서와 파일 순서
- filter 적용 행만 포함되는지
- 숨김·선택 열 처리
- source가 export 중 바뀌어도 시작 시점 snapshot이 유지되는지
- batch 누적 규칙과 아직 조회하지 않은 미래 batch 제외

## 9. 기존 TC 중복과 Lifecycle

신규 TC를 만들기 전에 title, steps, expected, change, dataset과 상태 전이를 비교한다.

- 같은 실패를 검출하면 기존 TC를 reuse하거나 필요한 필드만 update한다.
- 기능이 제거돼도 실행 이력이 있는 TC를 삭제하지 않는다.
- 제거된 TC는 `lifecycle.status: retired`로 전환한다.
- 신규 run snapshot에서는 retired TC를 제외한다.
- 전체 Excel에는 run 범위 밖 TC를 `N/A`로 투영하고 Not Run 집계에서 제외한다.

## 10. Run 생성과 결과 승계

새 run은 명시적 승인 뒤 생성한다.

1. 현재 active TC ID를 `test_case_refs`로 snapshot한다.
2. 같은 release의 직전 run을 찾는다.
3. 영향받지 않은 recorded result만 승계한다.
4. 정의가 바뀐 TC는 `PENDING`으로 둔다.
5. 신규 TC는 blank `Not Run`으로 둔다.
6. 이전 Not Run은 결과를 발명하지 않는다.
7. inherited FAIL은 `이번 차수 미확인`으로 구분한다.
8. 현재 run에서 같은 FAIL을 다시 저장하면 상태는 유지하되 inheritance 표식을 제거한다.

첫 patch의 FAIL을 과거 run에서 PASS로 덮어쓰지 않는다. 후속 patch 실행 증거는 후속 run에 기록한다.

## 11. Jira Reconciliation

Jira를 사용하는 경우 exact project와 exact Fix Version을 잠근다.

```text
Fix Version 전체 Jira key
= run들에 연결된 Jira key 합집합
+ 승인된 제품 범위 제외 key
```

기본 상태 정책 예시:

```text
Fix Version name contains Postpone -> POSTPONE
Fix Version name contains Pending  -> PENDING
Status Closed                      -> PASS, only in a later executed patch run
Status Open/Reopen/Reopened/Resolved -> FAIL 유지
그 외 또는 조회 실패               -> 자동 변경 금지
```

각 write는 다음 순서로 수행한다.

1. TC와 run의 현재 값을 GET한다.
2. 현재 ETag를 얻는다.
3. 상태 정책의 입력과 판정 결과를 audit에 기록한다.
4. `If-Match`로 PUT한다.
5. 응답 성공만 믿지 않고 다시 GET한다.
6. status, Jira link, revision이 기대값과 같은지 비교한다.
7. 변동 목록과 미변동 사유를 보고한다.

ETag 충돌, Jira 조회 실패, 한 TC의 run별 Jira key 충돌이 있으면 자동 갱신을 중단한다.

## 12. 웹 Write와 Cache Invalidation

웹 write의 완료 조건:

```text
authorize
-> validate candidate
-> compare revision / ETag
-> backup expected sources
-> atomic replace
-> explicit invalidate
-> reload candidate
-> validate reloaded catalog
-> publish new revision
-> GET and UI reread verification
```

후보 reload가 실패하면 last-known-good를 유지하고 `stale=true` 또는 명확한 persisted-but-stale 오류를 노출한다. 프로세스 재시작은 원인 수정이 아니라 복구 수단이다. 정상 write는 재시작 없이 새 revision이 보여야 한다.

## 13. Excel 생성과 최종 검증

Excel은 사용자가 명시적으로 요청한 경우에만 생성한다.

1. 선택 run과 release metadata를 고정한다.
2. YAML quality Gate를 먼저 통과한다.
3. workbook을 생성한다.
4. formula, merge, validation, conditional formatting, hyperlink와 blank result cell을 구조적으로 검사한다.
5. 설치된 최종 권위 앱으로 실제 연다.
6. repair 또는 compatibility 경고가 없는지 확인한다.
7. 두 sheet의 layout, status color, testing date, Jira, comments와 blank/white 기본 상태를 확인한다.

회사 기준이 Excel 2013이면 LibreOffice 렌더링은 최종 XLSX 합격 근거로 사용하지 않는다. PDF는 별도 요청과 별도 검증 대상이다.

## 14. 검증 명령

저장소가 동일한 script 이름을 제공할 때만 사용한다.

```powershell
pnpm tc:quality
pnpm test
pnpm test:e2e
pnpm assets:instances:audit
pnpm runs:jira:sync -- --project <PROJECT> --release <RELEASE> --run <RUN>
```

필수 결과:

- schema와 semantic validation 오류 0
- 참조 무결성 오류 0
- 제품 identity와 release/run scope 일치
- desktop E2E에서 browser console/page error 0
- edit/create/clone, result save, ETag conflict, stale/recovery 검증
- write 후 API와 UI가 같은 revision과 값을 노출
- Jira PUT 뒤 재조회 값 일치
- 최종 Excel 앱에서 repair/compatibility warning 없음

Windows에서 전체 test가 일시적 파일 rename `EPERM`으로 실패하면 해당 test를 한 번 단독 재실행한 뒤 전체 suite를 다시 실행한다. 두 번째 전체 실행 전에는 코드 결함으로 단정하지 않는다.

## 15. Claude Review 실행 규칙

- review packet에는 task, acceptance criteria, product/branch boundary, base/head, changed files, sanitized diff, authority docs와 validation evidence만 넣는다.
- credential, environment file, 내부 URL, 고객 데이터, unrelated user file을 넣지 않는다.
- read-only review를 기본으로 한다.
- 사고과정, 중간 JSON과 token stream을 수집하지 않는다.
- 최대 20~30분의 wait window를 주고 최종 텍스트 결과만 읽는다.
- finding은 현재 변경에서 실제로 도입됐는지 base/head semantic comparison으로 검증한다.
- 각 finding에 `accepted`, `rejected`, `deferred`와 근거를 남긴다.

## 16. Stop Conditions

다음 상황에서는 자동 mutation, run 생성, Jira 변경 또는 Excel 생성을 중단한다.

- product, release, branch, Fix Version, patch identity 중 하나가 불명확함
- 릴리즈 노트와 실제 UI 또는 코드 증거가 충돌함
- 핵심 주장이 `inference`, `recommendation`, `unresolved` 상태임
- 제품 밖 TC, dataset 또는 용어가 발견됨
- 기존 TC와 중복 여부를 판정할 수 없음
- ETag 또는 catalog revision이 변경됨
- 후보 catalog reload/validation이 실패함
- Jira key가 run마다 충돌하거나 조회가 완전하지 않음
- 최종 Excel 앱이 repair 또는 compatibility 경고를 출력함
- 사용자 파일이나 미요청 산출물이 변경 범위에 섞임

중단 보고에는 막힌 지점, 확인한 증거, 수행하지 않은 write와 필요한 사용자 결정을 적는다.

## 17. Handoff 형식

```markdown
# TC Automation Handoff

## Scope
- Product:
- Repository / branch:
- Release / Fix Version:
- Patch / run:

## Evidence
- Confirmed:
- Inference:
- Recommendation:
- Unresolved:

## Proposed Changes
- Reused TC:
- Updated TC:
- New TC:
- Retired TC:

## Run And Jira
- Previous run:
- Inherited:
- PENDING:
- Not Run:
- Jira changed / unchanged / blocked:

## Verification
- Quality:
- Unit/integration:
- Desktop E2E:
- Cache/revision reread:
- Excel final-app check:

## Files
- Changed:
- Generated:
- Deliberately untouched:

## Remaining Risks And Decisions
- ...
```

## 18. 금지사항

- 근거 없는 기대 결과 또는 데이터를 발명하지 않는다.
- 다른 제품 TC를 유사하다는 이유로 복사하지 않는다.
- 실행 이력이 있는 TC를 물리 삭제하지 않는다.
- 과거 run 결과를 후속 patch 결과로 덮어쓰지 않는다.
- ETag 없이 write하거나 충돌 뒤 자동 재시도하지 않는다.
- cache stale을 숨기기 위해 무조건 프로세스만 재시작하지 않는다.
- 사용자가 요청하지 않은 Excel/PDF를 생성하지 않는다.
- 최종 XLSX 검증 앱을 다른 office suite로 대체하지 않는다.
- Claude finding을 로컬 검증 없이 사실로 기록하지 않는다.
- credential, 내부 주소, 고객명, Jira key와 실제 데이터 경로를 공개 산출물에 넣지 않는다.
