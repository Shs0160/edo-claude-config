---
name: project_context
description: EDO 제품 개요 — 기획자 관점 제품 이해, 핵심 기능, 문서 구조, 이터레이션 현황
type: project
originSessionId: 7da8aaa5-d46f-4037-af0d-5f0df72435e2
---
# EDO 제품 컨텍스트 (기획자 기준)

**최종 업데이트:** 2026-04-23

## 제품 한 줄 정의

EDO(Enforcement Decision Orchestrator)는 문서 보안 제품군(SHIELD Drive, SHIELD Gate 등)에서 공통으로 사용할 **조건부 정책 집행 워크플로우를 중앙에서 관리**하는 오케스트레이션 플랫폼.

---

## 핵심 개념 구조

```
[조건부 정책] → (매핑) → [템플릿] → 집행 동작
      ↑
  타 서비스(SHIELDDrive 등)에서 정책 등록 시 EDO iframe 연동
```

- **조건부 정책**: 특정 조건에 따라 집행될 정책. 외부 서비스(SHIELDDrive 등)에서 생성
- **템플릿**: 재사용 가능한 집행 동작 구성 단위. Draft/Published 2단계 상태 관리
- **매핑**: 트리거(파일 업로드, 이메일 첨부 등) ↔ 템플릿 연결. 전사 공통(기본 매핑) + 정책별 개별 매핑
- **집행 워크플로우**: 서비스 페이지 내 iframe 형태로 삽입되어 동작하는 EDO UI

---

## 현재 이터레이션: I17

**기간:** 2026.03.23 ~ 2026.04.17 (진행중)  
**제목:** 템플릿 매핑 기반 Iframe 연동 구현

| 항목 | 상태 |
|---|---|
| 조건부 정책 JSON validation | 개발 중 |
| 집행 워크플로우 Iframe 제공 | 개발 중 |
| 템플릿 페이지(관리) 기능 | 개발 중 |
| 정책-템플릿 매핑 관리 | 개발 중 |

---

## 기완료 이터레이션 요약

| 이터레이션 | 주요 내용 |
|---|---|
| I16 (2026.03.09~03.20) | 조건부 정책 UI 최신화 (아이콘·색상·검색필터·용어 통일) |
| I15 (2025.12.31~01.27) | 조건부 정책 등록/목록 화면 신규 개발, 집행 정책 결과(Result) 필드 정의 |
| I14 (2025.12.10~12.30) | UI 설계 및 개발 기초 구축 |

---

## 핵심 기획 문서 위치

| 문서 | 경로 |
|---|---|
| 릴리즈노트 (단일 파일) | `edo_docs/docs/06.internal/01.planning/01.release_note.md` |
| 로드맵 | `edo_docs/docs/06.internal/01.planning/02.roadmap.md` |
| 이터레이션 MoM | `edo_docs/docs/06.internal/01.planning/05.MoM/` |
| 기능 스펙 | `edo_docs/docs/06.internal/01.planning/06.function/` |
| 유스케이스 | `edo_docs/docs/06.internal/01.planning/03.UseCase/` |

---

## 최근 확정된 기획 결정사항

1. **템플릿 상태 관리**: Draft / Published 2단계. 배포 성공 시 Draft 제거. 원복은 PUBLISHED_AND_DRAFT 상태에서만 허용
2. **서비스 연동 iframe 흐름 (SHIELDDrive)**: 조건부 정책 필수항목 입력 완료 후 카드 활성화 → 카드 클릭 시 자동 1차 저장 → 정책 ID 발급 → iframe 로드
3. **정책 목록 화면**: N+1 문제로 집행 정책 컬럼 1차 미표시. 정책 상세 화면에서만 트리거 연동 정보 표시
4. **데이터 구조**: `[정책] - [매핑] - [템플릿]`. 정책 엔티티에는 매핑/템플릿 정보 미포함

---

## 기술 스택 (참고용)

- **백엔드**: Java/Spring (edo/)
- **프론트엔드**: TypeScript + Vite (edo-fe/)
- **문서 사이트**: Docusaurus (edo_docs/)
- **CI/CD**: Azure Pipelines / Jenkins
