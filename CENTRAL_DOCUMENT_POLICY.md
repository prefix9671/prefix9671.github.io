# 중앙 문서 정책: Resume / Portfolio Evidence Pack

Last updated: 2026-06-21

## 1. 적용 범위

이 정책은 다음 파일에 적용한다.

- `index.html` (GitHub Pages 게시용 HTML; 원본 팩 파일 `ai_native_release_qa_resume_pack.html`의 게시 사본)
- `release_qa_detail.md`
- `CENTRAL_DOCUMENT_POLICY.md`
- `portfolio/` 아래의 공개용 포트폴리오 초안 문서

GitHub Pages에 게시할 때는 원본 팩 HTML을 `index.html`로 동기화해 사이트 대표 문서로 사용한다.

## 2. 문서 역할 정책

문서는 다음 역할로 분리한다.

| 영역 | 역할 | 공개 목적 |
|---|---|---|
| Page 1 | 이력서용 1-page summary | 채용 담당자 또는 빠른 검토자가 즉시 읽는 요약 |
| Page 2+ | 포트폴리오용 evidence pack | 프로젝트 문제, 접근, 실행, 결과, 자산화 근거 제시 |
| 상세보기 | 접힌 상세 근거 | 사용자 또는 필요한 검토자만 펼쳐보는 원문형 상세 설명 |

Page 1에는 핵심 포지셔닝, 운영 패턴, 대표 사례, 핵심 문장만 둔다.
Page 2 이후에는 포트폴리오로서 PAAR, 실패/통제, 자산화, 개선 로드맵을 배치한다.

## 3. 상세보기 공개 정책

상세보기는 HTML에서 기본적으로 접힌 상태여야 한다.

- HTML은 `<details>` 요소를 사용한다.
- `open` 속성을 기본으로 넣지 않는다.
- 상세 영역에는 `data-detail-default="collapsed"`를 유지한다.
- 정적 GitHub Pages는 인증 기반 접근 제어가 아니므로, 민감 정보나 고객 식별 정보는 상세 영역에도 넣지 않는다.
- 상세 정보가 필요한 사용자, 면접관, 내부 검토자만 수동으로 펼쳐 읽는 것을 전제로 한다.

## 4. Markdown 동기화 정책

`release_qa_detail.md`는 상세 근거의 원본이다.

- 상세 근거를 수정할 때는 먼저 `release_qa_detail.md`를 수정한다.
- HTML의 `#detail` 영역은 `release_qa_detail.md`를 렌더링한 결과와 의미적으로 동일해야 한다.
- Markdown과 HTML 상세 영역이 충돌하면 상세 근거는 Markdown을 기준으로 정정한다.
- HTML의 Page 1 및 Page 2+ 요약/포트폴리오 페이지는 요약본이므로 Markdown 원문과 문장이 완전히 같을 필요는 없지만, 사실관계와 용어는 일치해야 한다.

## 5. 게시 전 검수 체크리스트

게시 전 다음 항목을 확인한다.

- Page 1 footer가 `Page 1 · Resume`로 표시되는가.
- Page 2 이후 footer가 `Portfolio`로 표시되는가.
- 상세보기 `<details>`가 기본으로 접혀 있는가.
- 상세보기에서 `release_qa_detail.md`와 `CENTRAL_DOCUMENT_POLICY.md` 링크가 보이는가.
- Markdown 상단에 중앙 문서 정책과 HTML 동기화 대상이 명시되어 있는가.
- 고객명, 내부 URL, 비공개 티켓, 실제 장비 식별자 같은 민감 정보가 없는가.

## 6. 변경 절차

1. 상세 근거 수정 시 `release_qa_detail.md`를 먼저 고친다.
2. HTML의 접힌 상세 영역을 Markdown 내용과 동기화한다.
3. Page 1은 이력서 요약으로 유지한다.
4. Page 2 이후는 포트폴리오 근거 페이지로 유지한다.
5. 브라우저 또는 정적 검증으로 `<details>` 기본 접힘 상태와 주요 링크를 확인한다.
6. GitHub Pages 게시 전 최종 HTML과 Markdown을 함께 커밋한다.

## 7. 비공개 출처 기반 포트폴리오 초안 정책

비공개 저장소나 내부 업무 자료를 바탕으로 포트폴리오 초안을 만들 때는 다음 기준을 따른다.

- 공개 저장소에는 원문 내용, 고객명, 내부 제품명, 실행 환경값, 로그, 스크린샷, credential, 데이터베이스명, 내부 URL을 옮기지 않는다.
- 문서 구조, 역할 분리, 업데이트 정책, 에이전트 맥락 공유 의도처럼 공개 가능한 설계 패턴만 남긴다.
- 원본 저장소가 private이면 포트폴리오 문서에 그 사실을 명시하고, 공개본은 neutralized draft로 취급한다.
- `portfolio/` 아래 초안은 `index.html`에 승격되기 전까지 게시용 본문이 아니라 검토용 자료로 본다.
- 구조만 옮기는 경우 실제 원본 폴더명을 그대로 복제하기보다 `<program>`, `<runtime>`, `<report_validation>` 같은 일반화된 이름을 사용한다.
