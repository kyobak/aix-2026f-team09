
---
# 2주차 활동지 — 코딩 에이전트와 컨텍스트

| | |
| :-- | :-- |
| 팀명 |굳건|
| 작성일 |2026-09-11|
| 참여자 |박재원, 박지성, 강태혁, 전시준|

---

## 0. 준비

 `memo-seed` 저장소를 엽니다. 다음 파일이 있는지 확인하세요.

- [o] `schema.sql`
- [o] `service.js`
- [o] `routes.js`
- [o] `CONVENTIONS.md`

---

## 1. 조 나누기

팀을 두 조로 나눕니다. (4인 → 2:2 / 3인 → 1:2)

| 조 | 참여자 |
| :-- | :-- |
| A조 |강태혁, 전시준|
| B조 |박재원, 박지성|

**두 조는 같은 과제를 동시에 수행합니다.** 서로의 화면을 보지 마세요.

### 오늘의 과제 (두 조 공통)

> 메모 검색 기능을 추가하라. 제목과 본문에서 키워드로 찾을 수 있어야 한다.

---

## 2. 에이전트에게 준 것

### A조 — 이것만 붙여넣습니다

```
메모 검색 기능 만들어줘. 제목이랑 본문에서 키워드로 찾을 수 있게.
```

파일은 **하나도 주지 않습니다.**

### B조 — 네 칸을 모두 채웁니다

```
[지시]
메모 검색 기능을 추가해줘. 제목과 본문에서 키워드로 검색된다.

[규약]
(CONVENTIONS.md 내용 전체를 붙여넣기)

[근거]
(schema.sql, service.js, routes.js 내용 전체를 붙여넣기)

[종료조건]
- GET /memos/search?q=키워드 로 호출된다
- 제목 또는 본문에 키워드가 포함된 메모만 반환한다
- 본인 메모만 반환한다
- q가 비어 있으면 400과 { ok: false, error } 를 반환한다
```

### 실제로 붙여넣은 것 (원문 그대로, 요약 금지)

```
A조: 메모 검색 기능 만들어줘. 제목이랑 본문에서 키워드로 찾을 수 있게.
```
B조 박재원 : [지시]
메모 검색 기능을 추가해줘. 제목과 본문에서 키워드로 검색된다.

[규약]
(CONVENTIONS.md 내용 전체를 붙여넣기)

[근거]
(schema.sql, service.js, routes.js 내용 전체를 붙여넣기)

[종료조건]
- GET /memos/search?q=키워드 로 호출된다
- 제목 또는 본문에 키워드가 포함된 메모만 반환한다
- 본인 메모만 반환한다
- q가 비어 있으면 400과 { ok: false, error } 를 반환한다
> 요약하지 마세요. 나중에 이 기록이 무엇이 결과를 만들었는지 확인하는 근거가 됩니다.
> 완성형 파일 html 혹은 js 로 만들어줘.
> 
> +)파일 자체를 클로드 코드에 추가

B조 박지성 : [지시]
메모 검색 기능 만들어줘. 제목이랑 본문에서 키워드로 찾을 수 있게.
[지시]
메모 검색 기능을 추가해줘. 제목과 본문에서 키워드로 검색된다.

[규약]
프로젝트 규약

이 문서는 코드를 작성할 때 지켜야 할 규칙입니다.

계층 분리

- `routes.js`는 HTTP 요청과 응답만 다룹니다. SQL을 직접 쓰지 않습니다.
- 데이터베이스 접근은 `service.js`에만 둡니다.

응답 형식

모든 응답은 다음 두 형태 중 하나입니다.

{ "ok": true,  "data": ... }
{ "ok": false, "error": "ERROR_CODE" }

에러 코드는 대문자와 밑줄로 씁니다. (예: `MEMO_NOT_FOUND`)

명명 규칙

- 함수명은 동사로 시작합니다. `list`, `get`, `create`, `update`, `remove`
- 데이터베이스 컬럼은 스네이크 케이스를 씁니다. `user_id`, `created_at`
- 자바스크립트 변수는 카멜 케이스를 씁니다. `userId`, `createdAt`

입력 검증

- 사용자 입력은 반드시 검증합니다.
- 검증에 실패하면 400과 함께 `{ ok: false, error }` 를 반환합니다.

권한

- 모든 조회와 수정은 **본인 소유 데이터로 한정**합니다.
- 모든 쿼리에 `user_id` 조건을 포함합니다.

[근거]
-- memo-seed 데이터베이스 스키마

CREATE TABLE users (
  id         INTEGER PRIMARY KEY,
  email      TEXT NOT NULL UNIQUE,
  name       TEXT NOT NULL,
  created_at TEXT NOT NULL
);

CREATE TABLE memos (
  id         INTEGER PRIMARY KEY,
  user_id    INTEGER NOT NULL,
  title      TEXT NOT NULL,
  body       TEXT NOT NULL,
  created_at TEXT NOT NULL,
  FOREIGN KEY (user_id) REFERENCES users(id)
);

CREATE INDEX idx_memos_user ON memos(user_id);

const db = require('./db');

/**
 * 사용자의 메모 목록을 최신순으로 조회한다.
 */
function listMemos(userId) {
  return db.all(
    `SELECT id, title, created_at
       FROM memos
      WHERE user_id = ?
      ORDER BY created_at DESC`,
    [userId]
  );
}

/**
 * 메모 한 건을 조회한다. 본인 메모가 아니면 null을 반환한다.
 */
function getMemo(userId, memoId) {
  return db.get(
    `SELECT id, title, body, created_at
       FROM memos
      WHERE id = ? AND user_id = ?`,
    [memoId, userId]
  );
}

/**
 * 메모를 생성한다.
 */
function createMemo(userId, title, body) {
  return db.run(
    `INSERT INTO memos (user_id, title, body, created_at)
     VALUES (?, ?, ?, datetime('now'))`,
    [userId, title, body]
  );
}

module.exports = { listMemos, getMemo, createMemo };

const express = require('express');
const service = require('./service');

const router = express.Router();

// 메모 목록 조회
router.get('/memos', async (req, res) => {
  const memos = await service.listMemos(req.user.id);
  res.json({ ok: true, data: memos });
});

// 메모 단건 조회
router.get('/memos/:id', async (req, res) => {
  const memo = await service.getMemo(req.user.id, req.params.id);

  if (!memo) {
    return res.status(404).json({ ok: false, error: 'MEMO_NOT_FOUND' });
  }

  res.json({ ok: true, data: memo });
});

// 메모 생성
router.post('/memos', async (req, res) => {
  const { title, body } = req.body;

  if (!title || !body) {
    return res.status(400).json({ ok: false, error: 'TITLE_AND_BODY_REQUIRED' });
  }

  const result = await service.createMemo(req.user.id, title, body);
  res.status(201).json({ ok: true, data: { id: result.lastID } });
});

module.exports = router;

[종료조건]
- GET /memos/search?q=키워드 로 호출된다
- 제목 또는 본문에 키워드가 포함된 메모만 반환한다
- 본인 메모만 반환한다
- q가 비어 있으면 400과 { ok: false, error } 를 반환한다
---

## 3. 결과 확인

|A조| 확인 항목 | 결과 |
| :-: | :-- | :-- |
| ① | 실행 성공까지 걸린 시간 | 10분 27초 |
| ② | 없는 함수·컬럼을 지어낸 개수 | 0개 |
| | → 지어낸 이름 | |
| ③ | `CONVENTIONS.md` 위반 개수 | 5개 |
| | → 무엇을 어겼는가 |아래 표 참고|
| ④ | 사람이 직접 고친 지점 | 0곳 |
| | → 어디를 어떻게 | |
| ⑤ | **본인 메모만 반환되는가** | 아니오 |



|  번호 | 위반 내용    | 확인 결과                                                         |
| :-: | :------- | :------------------------------------------------------------ |
|  1  | 계층 분리    | `routes.js`와 `service.js`에 기능을 추가하지 않고 별도 웹앱으로 제작함            |
|  2  | 응답 형식    | `{ ok: true, data }` 또는 `{ ok: false, error }` 형식의 API 응답이 없음 |
|  3  | 입력 검증    | 빈 `q`에 대해 HTTP 400을 반환하는 검증이 없음                               |
|  4  | 권한 처리    | SQL과 `WHERE user_id = ?` 조건이 아예 없음                            |
|  5  | 함수 명명 규칙 | `dateLabel`, `fullDateLabel`, `filteredNotes` 등이 동사로 시작하지 않음  |

|B조 : 박재원| 확인 항목 | 결과 |
| :-: | :-- | :-- |
| ① | 실행 성공까지 걸린 시간 | 45분 30초 |
| ② | 없는 함수·컬럼을 지어낸 개수 | 0개 |
| | → 지어낸 이름 | |
| ③ | `CONVENTIONS.md` 위반 개수 | 0개 |
| | → 무엇을 어겼는가 ||
| ④ | 사람이 직접 고친 지점 | 1곳 |
| | → 어디를 어떻게 | `LIKE` 패턴에 `ESCAPE` 처리를 추가했다. 종료조건에는 없는 항목이다. |
| ⑤ | **본인 메모만 반환되는가** | 예 |

|B조 : 박지성 | 확인 항목 | 결과 |
| :-: | :-- | :-- |
| ① | 실행 성공까지 걸린 시간 | 38 분 |
| ② | 없는 함수·컬럼을 지어낸 개수 | 4 개 |
| | → 지어낸 이름 | db.js, app.js, init.js, package.json |
| ③ | `CONVENTIONS.md` 위반 개수 | 3개 |
| | → 무엇을 어겼는가 | 1. init.js가 service.js 밖에서 SQL을 직접 실행 2. app.js가 사용자 식별값을 쿼리 파라미터에서 무검증으로 받음 3. app.js에 오류 처리기가 없어 예외 시 { ok, error } 대신 프로세스가 중단됨 |
| ④ | 사람이 직접 고친 지점 | 6곳 |
| | → 어디를 어떻게 | 1. service.js 수정 : searchMemos함수 추가 2. service.js 수정 : module.exports에 searchMemos포함 3. routes.js 수정 : Get /memos/search 추가 - /memos/:id위에 배치 4. db.js 추가 : Node내장 sqlite 래퍼 5. app.js  추가 : 서버 진입점, 임시 사용자 식별 6. init.js + package.json 추가 : 테이블 생성/샘플데이터, 의존성 정의  |
| ⑤ | **본인 메모만 반환되는가** | 예 |



### ⑤번을 반드시 확인하세요

생성된 SQL에 `user_id` 조건이 들어 있는지 보세요. 없다면 코드는 정상 동작하지만 남의 메모까지 검색됩니다. 에러도 나지 않습니다. 

B조 박재원 : 생성된 SQL에 `WHERE user_id = ?` 조건이 들어 있습니다. 실제로 확인했습니다
— 같은 키워드 `라이선스`로 검색했을 때 1번 사용자는 본인 메모 1건, 2번 사용자는 1번 메모가 하나도 안 나오고 본인 메모 1건만 반환됐습니다.


---

## 4. 두 조의 결과 비교

작업이 끝나면 두 조가 만든 코드를 나란히 놓고 함께 답하세요.

**4-1. 두 결과의 가장 큰 차이는 무엇입니까?**

```
Convention.md의 파일 조건을 따라서 만들었는가와 아닌가의 차이점이 발생한다고 생각합니다.
```

**4-2. A조의 실패는 모델 탓입니까, 우리가 주지 않은 탓입니까? 근거를 들어 적으세요.**

```
A조의 실패는 모델 자체의 능력 부족보다는 필요한 프로젝트 정보를 제공하지 않은 것이 주된 원인이다. 기존 코드, 데이터베이스 스키마, 프로젝트 규약과 종료조건을 주지 않았기 때문에 에이전트는 요청을 독립적인 메모 앱 제작으로 해석했다. 그 결과 검색 화면은 구현했지만 GET /memos/search API, 빈 검색어 검증, 응답 형식, user_id 권한 조건은 구현하지 못했다. 이는 지시만으로는 기존 프로젝트의 구조와 숨겨진 요구사항을 정확히 추론할 수 없다는 것을 보여준다.

```

**4-3. B조가 준 자료 중 결과를 가장 크게 바꾼 것 하나를 꼽는다면 무엇입니까? 왜 그렇게 생각합니까?**

```
`CONVENTIONS.md`가 막아준 것들(응답 형식, 계층 분리, `user_id` 조건)은 틀려도 에러로 드러나거나 리뷰에서 잡히는 종류다. 반면 라우트 순서는 문법도 규약도 어기지 않고, 코드만 보면 멀쩡하며, 실행해야만 404로 나타난다. 그마저도 에러 메시지가 `MEMO_NOT_FOUND`라 검색 기능이 아니라 데이터 문제로 오해하기 쉽다.

`routes.js`를 줬기 때문에 에이전트가 기존 라우트의 순서를 보고 검색 라우트를 `:id` 위에 넣을 수 있었다.
규약은 "무엇을 지켜야 하는가"를 알려주지만, 기존 코드는 "지금 어떻게 생겼는가"를 알려준다. 후자가 없으면 맞는 규칙을 지키면서도 안 돌아가는 코드가 나온다.
```

---

## 5. PROMPTS.md 기록

위 2번의 프롬프트 원문을 저장소의 `PROMPTS.md`에 추가하고 커밋하세요.

```markdown
## 2026-__-__ · 메모 검색 기능 (2주차 활동)

**지시**
(붙여넣은 프롬프트 원문)

**채택 여부**
(전체 채택 / 일부 채택 — 무엇을 어떻게 수정했는지 / 미채택)

**참고**
(있으면)
```

- [o] `PROMPTS.md`에 추가하고 커밋했습니다

---

## 6. 제출 확인

- [o] 이 활동지를 저장소에 커밋했습니다
- [o] `PROMPTS.md`를 커밋했습니다

