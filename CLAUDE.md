# 우리 아기 육아수첩

아이(2027년 2월 출생 예정)를 위한 육아 기록 웹앱. 사용자는 갤럭시 폴드8 울트라 브라우저에서 사용하며, 모바일(Claude 앱 / claude.ai/code)에서 이어서 수정 요청을 하는 경우가 많다. 답변은 한국어로.

## 배포
- 소스: `baby-note.html` (단일 파일, 빌드 없음)
- 게시된 Artifact: https://claude.ai/artifact/Pe2P3sSQErBeAo75q2mvt4
- 수정 후 반드시 **같은 URL로 재게시**한다 (`Artifact` publish에 `url` 지정). 새 대화에서는 publish 전에 `action: "read"`로 현재 버전을 먼저 읽고, 로컬 파일과 다르면 게시된 버전을 기준으로 맞춘다.
- `capabilities`는 `{db: {}, downloads: true}`. 재게시 때는 생략해서 유지한다(바꿀 때만 전체를 다시 지정).
- 페이지 앞부분의 `<title>`, 아이콘(baby)은 유지.

## 데이터 (Artifact db — 실제 가족 기록이므로 스키마 변경 시 기존 데이터 호환 필수)
- `meta/profile` `{name, sex, due:"YYYY-MM-DD", birth:"YYYY-MM-DDTHH:MM"}`
- `meta/timers` `{feed:{start,side,sides}|null, sleep:{start}|null}`
- `meta/growth` `{items:{<id>:{d,w,h,hc,del?}}}`
- `meta/vax` `{done:{<key>:"YYYY-MM-DD"|""}}` — 접종·검진 완료
- `meta/prep` `{done:{<key>:bool}, custom:{<id>:{g,label,del?}}}`
- `days/<YYYY-MM-DD>` `{date, ev:{<id>:{k,t,memo,...,del?}}}` — 하루 1문서, 이벤트는 map에 merge(`update`)로 추가해 부부 동시 기록 충돌을 피한다. 삭제는 `del:true` 톰스톤.
- `letters/<YYYY-MM>` `{month, items:{<id>:{who:"mom"|"dad", t, text, del?}}}` — 엄마·아빠가 아이에게 남기는 한마디(한마디 탭). 한 달 1문서, `update` merge로 추가, 삭제는 톰스톤.
- 이벤트 종류 `k`: breast(side,min) bottle(src,ml) pump(side,ml) diaper(pee,poo,color) sleep(end) bath temp(c) med(name,dose) note
- db 한도: 아티팩트당 문서 5,000개 → 이벤트 1건=1문서로 바꾸지 말 것.
- db를 못 쓰는 환경(파일 직접 열기)에서는 localStorage(`bn-data`)로 폴백.

## 디자인 규칙
- 색은 `:root` 토큰만 사용, 다크 모드 블록 2개 유지. 야간 버튼은 `data-theme="dark"`를 직접 설정.
- 접힌 화면(~400px) 하단 탭, 760px 이상 좌측 레일 + 900px 이상 2단.
- `alert/confirm/prompt` 금지(뷰어에서 동작 안 함) → 두 번 눌러 확인하는 방식 사용.
- 의료 정보(접종 일정, 발열·변 색 안내)는 참고용 문구와 예방접종도우미 링크 유지.
