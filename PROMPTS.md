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

## 2026-09-11 · 메모 검색 기능 (2주차 활동)

**목표(스펙) / Spec**
- 입력 Input: 검색 키워드 `q` (쿼리스트링), 요청자 `user_id`
- 처리 Processing: 본인 소유 메모 중 **제목 또는 본문**에 키워드가 포함된 것을 찾아 최신순 정렬
- 출력 Output: `{ ok: true, data: [{ id, title, created_at }] }`
- 실패 조건 Failure: `q`가 비었거나 공백뿐이면 `400` / `{ ok: false, error: "SEARCH_QUERY_REQUIRED" }`

**요청한 프롬프트 요지 / Prompt (summary)**

네 블록으로 나눠 제시했다.
`[지시]` 한 줄 · `[규약]` CONVENTIONS.md 전문 · `[근거]` schema.sql·service.js·routes.js 전문 ·
`[종료조건]` 네 항목(호출 형태 / 매칭 범위 / 본인 메모 한정 / 빈 입력 처리).

원문은 요약 없이 `memo-seed/B조-프롬프트-원문.txt` (146줄)에 보관했다.

**결과에 대한 판단 / Decisions**

- 채택한 부분과 이유 / Accepted, because:
  `service.js`의 `searchMemos`와 `routes.js`의 `GET /memos/search`를 그대로 받았다.  규약 네 항목(계층 분리 · 응답 형식 · 명명 규칙 · `user_id` 조건)을 모두 지켰고,  존재하지 않는 컬럼이나 함수를 지어낸 곳이 없었다.  특히 **`/memos/search`를 `/memos/:id`보다 위에 배치한 점**을 그대로 받아들였다.  Express는 등록 순서대로 먼저 일치하는 라우트가 이기므로, 아래에 두면 `search`가`:id` 값으로 잡혀 검색이 항상 `404 MEMO_NOT_FOUND`가 된다.  `[근거]`에 `routes.js` 전문을 넣었기 때문에 가능했던 판단이라고 본다.

- 수정한 부분과 이유 / Changed, because:
  `LIKE` 패턴에 `ESCAPE` 처리를 추가했다. 종료조건에는 없던 항목이다.  키워드에 `%`나 `_`가 들어오면 SQL 와일드카드로 해석돼 의도와 달리 전체가 매칭된다. `%`·`_`·`\`를 문자 그대로 다루도록 바꿨다.  종료조건 밖의 판단이므로 여기에 근거를 남긴다.

- 폐기한 부분과 이유 / Rejected, because:
  없음.

  **검증 방법 / How it was verified**

예시 데이터(사용자 2명, 메모 8건)를 넣고 7개 시나리오를 실행해 확인했다.

| 확인한 것 | 결과 |
| :-- | :-- |
| 제목 매칭 — `q=라이선스` (user 1) | `라이선스 조사 메모` 1건 |
| **본문** 매칭 — `q=404` (user 1) | `라우트 순서 주의` 1건 (제목엔 없는 단어) |
| 권한 — `q=라이선스` (user 2) | 1번 사용자 메모 **0건**, 본인 것만 1건 |
| 빈 입력 — `q=` | `HTTP 400` / `SEARCH_QUERY_REQUIRED` |
| ESCAPE — `q=%` | 전체 7건이 아니라 `%`가 실제로 든 1건 |
| 라우트 순서 — `GET /memos/3` | `HTTP 200` (단건 조회가 깨지지 않음) |
| 전체 목록 — `GET /memos` | 7건, 최신순 |
