# mission

TodoCalendar 의 계획–명령 체계 하네스(작업 지침·캠페인 계획·릴리즈 계획과 그 보고 계약)를 떼어 낸 Claude Code plugin 이다. 레포 하나에 plugin 하나만 싣고, 루트의 `.claude-plugin/marketplace.json` 이 이 레포 자신을 가리킨다. GitHub 레포는 [sudopark/mission](https://github.com/sudopark/mission) 이고 기본 브랜치는 `main` 이다. 첫 설치처는 새로 시작할 프로젝트다.

## 정본 두 개

- **무엇을 옮기고 무엇을 남기나** — [sudopark/TodoCalendar#1098](https://github.com/sudopark/TodoCalendar/issues/1098) 본문이다. 이관 대상·분리가 필요한 스킬·함께 옮길 후보·선결 과제·미정이 여기 있다. 이 레포에서 경계를 새로 정하면 #1098 본문도 같이 고친다 — 두 레포의 세션이 같은 경계를 보는 자리가 그 본문 하나뿐이다.
- **옮길 실물** — 로컬 `~/Documents/codebase/TodoCalendar` 의 `origin/develop` 이다. 작업 트리가 아니라 `git show origin/develop:<경로>` 로 읽는다. 작업 트리에는 다른 세션이 커밋하지 않은 변경이 섞여 있을 수 있다.

## 처음 한 번 — 초기 이관

1. `git init` 하고 plugin 골격을 만든다: `.claude-plugin/plugin.json`(name `mission`), `.claude-plugin/marketplace.json`(plugin source `./`), `skills/`, `README.md`.
2. #1098 본문을 읽고 이관 대상을 `origin/develop` 에서 옮긴다. 프로젝트에 묶인 값(경로·스크립트·보드 배선)은 선결 과제 1·5 대로 주입받는 형태로 뒤집는다.
3. README 는 이 체계가 지휘 체계와 임무형 지휘에서 왔다는 기원을 설명한다. 본문 용어는 일반어를 쓴다(TodoCalendar 115fae2d·9a2b7558 의 치환을 따른다).
4. 옮긴 시점의 `origin/develop` sha 를 `UPSTREAM.md` 기준점에 적고 커밋한다.

## 매 세션 시작 — 동기화

TodoCalendar 는 이 plugin 의 원본을 계속 고친다. 세션을 열면 작업 전에 밀린 변경부터 확인한다.

1. `git -C ~/Documents/codebase/TodoCalendar fetch origin`
2. `git -C ~/Documents/codebase/TodoCalendar log --format='%h %ad %s' --date=short <기준점>..origin/develop -- .claude CLAUDE.md docs/operations/templates`
3. `gh issue view 1098 -R sudopark/TodoCalendar` 로 본문을 다시 읽는다. 경계 결정은 커밋 없이 본문만 바뀌기도 한다.
4. 커밋마다 `반영` 또는 `해당 없음` 으로 판정하고 `UPSTREAM.md` 기록 표에 한 줄씩 남긴다. 판정 기준은 #1098 의 이관 범위다. 프로젝트 종속 조각(테스트 스킴·tuist·보드 배선)만 바꾼 커밋은 `해당 없음` 이다.
5. 반영을 마치면 기준점을 그 `origin/develop` sha 로 올리고 커밋한다.

경로를 넓게 훑는 이유가 있다. 이관 대상 목록으로 좁혀 훑으면, 목록에 아직 없는 새 스킬이 계획 체계에 붙었을 때 놓친다.
