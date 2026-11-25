# 12주차 활동지 / Week 9 Worksheet

**개발 착수 / Starting development**

- 작성일 / Date: 2026-11-25 
- 참여자 / Present: 박재원, 전시준, 박지성, 강태혁 

---

## ① 착수 점검표 / Start checklist

- [x] 초기 파일이 저장소에 올라와 있다 / Initial files are in the repository
- [x] 7주차 이슈가 GitHub에 등록되어 있고 담당자가 지정되어 있다 / Week 7 issues are on GitHub with owners
- [x] 팀원 모두 같은 환경에서 실행된다 / Everyone can run it in the same environment
- [x] 브랜치 규칙을 정했다 / Branch rule agreed — GitHub Flow. main 직접 push 금지(설정으로 차단), 이슈마다 `종류/번호-설명` 브랜치(예: `feat/7-signal-fsm`), PR은 1명 승인 + CI(ruff·pytest) 통과 후 Squash merge (CONTRIBUTING.md 1·6절)

---

## ② 개발 일정 / Development schedule

| 주차 Week | 수업 일정 Class | 끝낼 이슈 # Issues to close | 담당 Owner |
|---|---|---|---|
| 9주차 | 개발 착수 | #1 SceneState·라벨 클래스, #2 카메라·영상 입력, #3 촬영 규약, #19 Slack 알림 | 박재원(#1·#19), 강태혁(#2), 전시준(#3) |
| 10주차 | 막힘 진단 · 데모 시나리오 초안 | #4 비식별·프레임 추출, #5 D0 검출기, #6 신호 색 판정, #7 신호 상태 머신, #8 음성·진동 안내, #9 전 구간 뼈대, #34 실시간 재생 | 강태혁(#4), 박지성(#5·#6), 박재원(#7·#9·#34), 전시준(#8) |
| 11주차 | **MVP 완료 · 중간 데모** | #10 데모용 디버그 화면 (D0 기반 MVP: 영상 → 검출 → 상태 → 안내 완성) | 전시준(#10) |
| 12주차 | 핵심 경로 테스트 | #11 라벨링·일치도, #12 D1, #13 D2, #44 폰 카메라 프레임 서버, #45 3구역 웹앱 | 전원(#11), 강태혁(#12·#44), 박지성(#13), 전시준(#45) |
| 13주차 | **배포 · 실행 패키지** | #15 D0·D1·D2 비교 평가, #46 현장 조사, #16 실행 패키지·README | 박재원(#15·#46), 전시준(#16) |

> #14 점자블록 오프셋(Should)은 11/23 범위에서 제외 → 5주차 Won't 목록으로 이동 조치

---

## ③ 이번 주 목표 이슈 / This week's issues

| # | 제목 Title | 담당 Owner | 완료 Done |
|---|---|---|---|
| #11 | 라벨링·두 사람 일치도 확인 | 전원 | [x] 라벨링 완료(1,067장), 일치도 확인 남음  |
| #12 | 사전학습 YOLO 검출기 (D1) | 강태혁 | [x] PR #41 리뷰 |
| #13 | Fine-tune YOLO (D2) | 박지성 | [x] 학습셋 구성·학습 진행 완료 |
| #44 | 폰 카메라 프레임 서버 | 강태혁 | [ ] PR #47 리뷰  |
| #45 | 화면 없이 쓰는 3구역 웹앱 | 박재원 | [ ]  |

---

## ④ 오늘 진행한 것 / Progress today
- [ ] 이번 활동에서 AI를 사용했다면 `PROMPTS.md`에 기록했습니다 / Logged any AI use in `PROMPTS.md` 

---

> 수업 종료 시 커밋하세요 / Commit this at the end of class
> `git add docs/week-09.md && git commit -m "docs: 129주차 활동지 작성"`
