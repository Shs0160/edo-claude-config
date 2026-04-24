---
description: 이터레이션 미팅 회의록 사전 준비. EDO 정규 운영 이터레이션 미팅 대상.
argument-hint: "[이터레이션 이름 — 생략 시 CLAUDE.md의 현재 이터레이션 사용]"
---

# 이터레이션 미팅 준비 (EDO)

EDO는 이터레이션 미팅을 정기 운영. 회의록은 `edo_docs/docs/06.internal/01.planning/05.MoM/` 폴더에 파일 단위로 추가.

## 인풋

- 대상 이터레이션: `$ARGUMENTS` (비어있으면 CLAUDE.md의 `### Azure DevOps > 현재 Iteration` 값 사용)

## 수집

1. **직전 미팅 액션아이템 회고**
   - `05.MoM/` 폴더의 가장 최근 회의록에서 액션아이템 추출 (또는 `memory/decisions.md`)
   - 각 항목의 현재 상태 (완료 / 진행 중 / 보류)

2. **현재 이터레이션 User Story 목록**
   - Azure DevOps에서 `IterationPath = {대상}` 조건으로 User Story 전체
   - 각 User Story 하위 Task 진척률 (Closed / Total)

3. **블로커 / 정체 항목**
   - `Blocked` 태그 또는 Priority=1
   - 3일 이상 In Progress 상태 변화 없음 → "정체 의심" 플래그

## 출력: 새 회의록 파일 초안

파일 경로 제안: `edo_docs/docs/06.internal/01.planning/05.MoM/{번호}.{주제_slug}.md`
- 번호: 기존 MoM 폴더의 다음 순번
- 주제 slug: 이터레이션 번호 + 간단 주제 (예: `02.i17_iframe_integration_review.md`)

## 포맷 (EDO 기존 MoM 스타일 + 회의록 섹션)

```
---
sidebar_position: {다음 순번}
title: {회의 주제}
tags:
  - EDO
  - MoM
---

# {회의 주제}

> **일시**: YYYY-MM-DD HH:MM
> **참석**: (빈 칸, 회의 전/후 채움)
> **대상 이터레이션**: I{n} - {제목}

<br />

## 1. 직전 액션아이템 회고
---

- [완료/진행중/보류] {항목} — {비고}

<br />

## 2. 이번 이터레이션 진척
---

| ID | 제목 | 진척 | 담당 | 이슈 |
|---|---|---|---|---|
| #{id} | {제목} | {closed}/{total} Task | {담당} | {있으면 기록} |

<br />

## 3. 블로커 / 정체 항목
---

- [#{id}] {제목} — {상태} / {원인 추정}

<br />

## 4. 논의 필요 주제 (사전 식별)
---

- {memory/decisions.md에 "논의 필요"로 남긴 항목, 블로커에서 파생된 주제}

<br />

## 5. 신규 액션아이템 (회의 중 채움)
---

- [ ] 
```

## 규칙

- EDO 기존 MoM 스타일(`<br />`, `---`, 섹션 헤더) 유지
- 담당자 정보 없으면 빈 칸 (추정 금지)
- 진척 0/N, N/N 항목은 표 하단에 묶음 (초점은 진행 중 항목)

## 완료 후

- 미팅 전 훑어보고 수정
- 미팅 후 결정/액션아이템은:
  - 해당 회의록 파일 "5. 신규 액션아이템" 섹션에 기록
  - 중요 의사결정은 `memory/decisions.md`에도 append (추적용)
