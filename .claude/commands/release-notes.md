---
description: 매주 월요일 루틴. 현재 이터레이션 진행 상황을 edo_docs의 "06.internal/01.planning/01.release_note.md" 단일 파일에 이터레이션 섹션으로 반영.
---

# 릴리즈노트 업데이트 (월요일 루틴)

## 대상 파일

`edo_docs/docs/06.internal/01.planning/01.release_note.md`

- **단일 파일** (SDF처럼 연도 분기 없음)
- frontmatter는 단순 (`sidebar_position`, `title`, `tags`). Docmost sync 없음 — 자유 수정 가능
- 본문 최상단 `**※ 최종 업데이트 : YYYY.MM.DD**`는 업데이트

## 수집

1. **Azure DevOps** — 현재 Iteration (`EnforcementDecisionOrchestrator\I{n} - ...`)
   - Work Item 전체 (User Story, Task) 제목 + 상태
   - IterationPath 시작/종료일
   - 카테고리 분류: 각 Work Item 제목에서 `[신규]`, `[개선]`, `[버그]` 등 접두가 있으면 그대로. 없으면 내용 기반 추정
2. **기존 `01.release_note.md` 읽기** — 이번 이터레이션 섹션이 이미 있는지 확인
3. **`memory/decisions.md`** — 이번 이터레이션 기간 의사결정

## 처리 로직

- **현재 이터레이션 섹션이 이미 있으면** → 해당 섹션 내용 갱신 (새로 완료된 항목 반영). `(진행중)` 유지
- **없으면** → 최상단(직전 이터레이션 섹션 위)에 신규 섹션 append
- **모든 Work Item이 Closed/Resolved** → 제목 꼬리표 `(진행중)` → `(완료)`

## 포맷 (기존 `01.release_note.md` 스타일 그대로)

```
### ◈ I{n} - {이터레이션 제목} ({진행중|완료})

> **기간:** YYYY.MM.DD ~ YYYY.MM.DD

**`{카테고리 — 신규|개선|버그|기타}`**

#### 1. {주제}
- {내용}
- {내용}

#### 2. {주제}
- {내용}

<br />

---

<br />
```

## 규칙

- **`vX.Y.Z` 버전 표기 금지**. `I{n}` 이터레이션 번호가 사실상 버전
- 카테고리 태그(`**\`신규\`**` 등)는 기존 섹션 스타일 참조. 필요 시 여러 개 병기 가능
- 내부 구현 용어 대신 기능 관점 표현
- 이모지·감탄사 금지
- `<br />` 태그와 `---` 구분선은 기존 포맷 그대로 유지

## 완료 후

1. 변경 diff를 송희수님께 보여주고 검토
2. 확인되면 저장
3. 본문 최상단 `**※ 최종 업데이트 : YYYY.MM.DD**`를 오늘 날짜로 갱신
4. `memory/changelog.md`의 [Unreleased] 블록도 동기화

## 범위 밖

- 배포 단위 상세 릴리즈노트(`edo/06.internal/02.development/release/release_note.md` 등 백엔드·프론트 폴더) — 실제 배포 시점에만 작성, 이 커맨드 대상 아님
