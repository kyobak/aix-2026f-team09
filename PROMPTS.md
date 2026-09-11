# AI 협업 기록 / AI Collaboration Log

작성 원칙: 프롬프트 나열이 아니라 **판단 근거**를 남긴다.
Principle: record your **reasoning**, not just prompts.

---

## [이슈 #__] 제목 / Title

**목표(스펙) / Spec**
- 입력 Input:
- 처리 Processing:
- 출력 Output:
- 실패 조건 Failure:

**요청한 프롬프트 요지 / Prompt (summary)**

**결과에 대한 판단 / Decisions**
- 채택한 부분과 이유 / Accepted, because:
- 수정한 부분과 이유 / Changed, because:
- 폐기한 부분과 이유 / Rejected, because:

**검증 방법 / How it was verified**

---
(이슈 단위로 반복 / repeat per issue)

## 2026-09-11 · 메모 검색 기능 (2주차 활동)

**지시**

메모 검색 기능 만들어줘. 제목이랑 본문에서 키워드로 찾을 수 있게.

**채택 여부**

미채택

에이전트가 기존 `memo-seed` 프로젝트에 검색 API를 추가하지 않고, 별도의 브라우저용 메모 앱을 새로 만들었다. 따라서 기존 `service.js`와 `routes.js`에 적용할 수 없었으며, 요구된 `GET /memos/search?q` 엔드포인트도 구현되지 않았다.

**참고**

기존 코드, 데이터베이스 스키마, 프로젝트 규약과 종료조건을 제공하지 않아 에이전트가 요청을 독립적인 메모 앱 제작으로 해석했다. 그 결과 제목과 본문을 검색하는 기능 자체는 작동했지만, 빈 검색어 검증, 규정된 API 응답 형식, `user_id`를 이용한 본인 메모 제한 조건은 반영되지 않았다.

