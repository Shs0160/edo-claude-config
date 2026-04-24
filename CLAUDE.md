# EDO — Claude 컨텍스트

> 이 파일은 Cowork/Claude Code가 세션 시작 시 자동으로 읽습니다.
> "공통 프로필" 섹션은 5개 제품 CLAUDE.md에 동일하게 들어가는 블록이며, 변경 시 다른 제품에도 동기화해야 합니다.

---

## GitHub 설정 파일 관리

### "깃 올려줘" 입력 시

아래 순서로 즉시 실행:

```bash
# 1. auto-memory 최신 내용 동기화
cp /c/Users/Security365/.claude/projects/C--Users-Security365-edo/memory/*.md /c/Users/Security365/edo/auto-memory/

# 2. 변경사항 스테이징 & 커밋 & 푸시
cd /c/Users/Security365/edo
git add .
git commit -m "chore: 설정 파일 업데이트 $(date '+%Y-%m-%d')"
git push
```

커밋 메시지는 변경 내용에 따라 적절히 수정.

### 새 머신에서 세팅하는 방법

1. **저장소 클론** — 빈 폴더 먼저 생성 후 클론
   ```bash
   mkdir -p /c/Users/<사용자명>/edo
   cd /c/Users/<사용자명>/edo
   git clone https://github.com/Shs0160/edo-claude-config.git .
   ```

2. **환경변수 설정** — `AZURE_DEVOPS_PAT` 등록 (Windows 시스템 환경변수)
   - 시스템 속성 → 고급 → 환경 변수 → 사용자 변수에 `AZURE_DEVOPS_PAT` 추가

3. **auto-memory 복원** — auto-memory 파일을 Claude가 읽는 위치로 복사
   ```bash
   # 경로의 <사용자명>을 실제 값으로 변경
   MEMORY_DIR="/c/Users/<사용자명>/.claude/projects/C--Users-<사용자명>-edo/memory"
   mkdir -p "$MEMORY_DIR"
   cp /c/Users/<사용자명>/edo/auto-memory/*.md "$MEMORY_DIR/"
   ```
   > 경로 인코딩 규칙: `-`는 `-`, `/`는 `-` (예: `C:\Users\foo\edo` → `C--Users-foo-edo`)

4. **코드 저장소 클론** — Azure DevOps에서 별도 클론
   ```bash
   cd /c/Users/<사용자명>/edo
   git clone <azure-devops-url>/edo          # 백엔드
   git clone <azure-devops-url>/edo-fe       # 프론트엔드
   git clone <azure-devops-url>/edo_docs     # 문서
   ```

5. **MCP 서버 설치 확인** — `.mcp.json`의 서버들이 실행 가능한지 확인
   ```bash
   # docmost MCP
   cd /c/Users/<사용자명>/source/repos/mcp-docmost
   npm install
   # azure-devops MCP는 npx로 자동 실행됨
   ```

---

## 세션 시작 프로토콜 ("시작" 입력 시)

사용자가 "시작"이라고 말하면 아래 순서로 즉시 실행:

1. **git pull** — 3개 저장소 최신 코드 받기 (병렬 실행)
   ```
   cd /c/Users/Security365/edo/edo && git pull            # 백엔드 (dev)
   cd /c/Users/Security365/edo/edo-fe && git pull         # 프론트 (dev)
   cd /c/Users/Security365/edo/edo_docs/docs && git pull  # 문서 (main)
   ```
2. **Docmost MCP 연결 확인** — `docmost_health` 도구 호출. 실패 시 사용자에게 알리고 중단
3. `memory/project_context.md` 읽기 — 제품 개요, 핵심 개념, 이터레이션 현황 파악
4. `memory/ongoing_work.md` 읽기 — 미결 작업, 다음 할 일 확인
5. 최신 상태 검증 (빠르게):
   - `edo_docs/docs/06.internal/01.planning/01.release_note.md` — 릴리즈노트 최상단 이터레이션 확인
   - `edo_docs/docs/06.internal/01.planning/06.function/` — 최근 기획 문서 확인
6. 사용자에게 현재 상태 요약 보고 (2~3문장): 현재 이터레이션, 미결 작업, 오늘 할 수 있는 것

> **코드는 필요할 때만 읽음.** 기획자 관점에서 문서/기능 상태 파악이 우선.

### 세션 종료 시 (작업 완료 또는 사용자 요청 시)

- `memory/ongoing_work.md` 업데이트: 완료 항목 체크, 새로 생긴 미결 사항 추가
- `memory/project_context.md` 업데이트: 새로 확정된 기획 결정사항이 있으면 추가

---

## Docmost 문서 작업 방법

> 문서 작성/수정 요청이 오면 아래 방법으로 Docmost에 직접 반영. 로컬 파일 초안 작성 후 수동 업로드 하지 않음.

### 핵심 정보

- **EDO 스페이스 ID**: `019d2271-792a-7d5c-817d-5e0b4754bf68`
- **인증**: Cookie 방식 (`authToken=<JWT>`). 토큰은 `~/.claude.json` mcpServers.docmost.env 참조
- **페이지 ID 맵 전체**: `memory/docmost_edo.md` 참조 (주요 페이지 ID 수록)

### 작업 유형별 방법

#### 기존 페이지 내용 읽기
```bash
TOKEN="<JWT>"
curl -s -X POST "https://docmost.softcamp.co.kr/api/pages/export" \
  -H "Content-Type: application/json" -H "Cookie: authToken=$TOKEN" \
  -d '{"pageId":"<ID>","format":"markdown"}' -o /tmp/page.zip
cd /tmp && unzip -o page.zip -d page_out && cat page_out/*.md
```

#### 페이지 내용 업데이트
```bash
# 1. 수정할 내용을 /tmp/content.md 파일로 작성
# 2. 스크립트 실행
cd C:/Users/Security365/source/repos/mcp-docmost
node update_page.mjs "<pageId>" "/tmp/content.md"
# 성공: { success: true, nodesInserted: N }
```

#### 페이지 ID 모를 때 — 전체 맵 갱신
```bash
TOKEN="<JWT>"
curl -s -X POST "https://docmost.softcamp.co.kr/api/spaces/export" \
  -H "Content-Type: application/json" -H "Cookie: authToken=$TOKEN" \
  -d '{"spaceId":"019d2271-792a-7d5c-817d-5e0b4754bf68","format":"markdown"}' \
  -o /tmp/edo_export.zip
unzip -o /tmp/edo_export.zip -d /tmp/edo_export
# /tmp/edo_export/docmost-metadata.json 에서 ID 확인
```

### 자주 수정하는 페이지 ID (빠른 참조)

| 페이지 | ID |
|---|---|
| 관리자 가이드/조건부 정책 관리 | `019d9b68-3d18-779b-80f1-b371973bf8a4` |
| 관리자 가이드/집행 워크플로우 관리 | `019d9b68-3d18-779b-80f1-b6080f8b326a` |
| 관리자 가이드/서비스 연동 관리 | `019d9b68-3d18-779b-80f1-ac15433bd71a` |
| 내부 문서/기획/릴리즈 노트 | `019d9b68-3d19-751d-9c97-0c7a89bf34b3` |
| 내부 문서/기획/기능 정의 | `019d9b68-3d1a-76e9-97a5-db4581294bee` |
| 내부 문서/기획/유스케이스 | `019d9b68-3d1a-76e9-97a5-cc8acba7cefa` |
| 내부 문서/기획/회의록 | `019d9b68-3d1a-76e9-97a5-d42546f3df05` |
| 제품 소개/주요기능 | `019d9b68-3d18-779b-80f1-9b85142235ce` |
| 제품 소개/용어집 | `019d9b68-3d18-779b-80f1-9fd279335ac4` |

---

## 공통 프로필 (모든 제품 공유 / sync-required)

- **담당자**: 송희수 (heesu.song@softcamp.co.kr)
- **역할**: 제품 기획자 — B2B SaaS 문서 보안
- **담당 제품**: DS for Mobile · SHIELD Drive · EDO · SDF · DS for Mac
- **핵심 업무**: 기능 기획 → 개발 항목/일정 관리 → 문의 대응

### 주기 업무
- **매주 월요일**: 진행중 개발 항목을 기반으로 각 제품 릴리즈노트 업데이트
- **매주 수요일**: EDO·SHIELD Drive 이터레이션 미팅 회의록 준비

### 출력/문서 규칙
- **Work Item**(User Story/Task/Bug) → Azure DevOps (공통)
- **모든 문서 (가이드·릴리즈노트·기획 문서 등)** → **Docmost에 직접 작성** (MCP 연동, `docmost_*` 도구 사용)
  - Docmost URL: `https://docmost.softcamp.co.kr`
  - 인증: Cookie 방식 (`authToken`). MCP 서버가 자동 처리하므로 별도 토큰 입력 불필요
  - 문서 작성 요청이 오면 즉시 Docmost에 페이지 생성/수정. 로컬 파일로 초안 작성 후 수동 업로드하지 않음
- **제품 docs 사이트 (Docusaurus)** → 내부 기획 문서 한정. 외부 배포 문서는 Docmost 기준
- 언어: 한국어 (코드/변수명은 영어 허용)
- 문체: 제품 문서 톤 (사실 중심, 불필요한 수식·감탄사·이모지 지양)
- 리스트 항목은 과하게 쪼개지 않고 의미 단위로 묶기

### Claude 작업 원칙
1. 새 세션에서 코드/구조를 재분석하기 전에 반드시 이 CLAUDE.md + `memory/` 4종 파일 먼저 읽기
2. 의사결정(스펙 변경·우선순위 조정 등) 발생 시 → `memory/decisions.md`에 날짜·배경·결론 기록
3. 문의 응대 패턴은 `memory/inquiries.md`에 누적 (같은 문의 반복 방지)
4. 릴리즈노트/이터레이션 산출물은 `.claude/commands/` 슬래시 커맨드로 생성

---

## 제품: EDO (Enforcement Decision Orchestrator)

### 개요
EDO는 문서 보안 제품군의 **조건부 정책 집행(Enforcement Policy) 오케스트레이션** 제품. 여러 서비스(SHIELD Drive, SHIELD Gate 등)에서 공통 사용할 정책을 중앙에서 관리하고, 정책·템플릿·매핑 기반으로 집행 워크플로우를 구성. 이터레이션 미팅을 정기적으로 운영하는 제품.

<!-- TODO: 핵심 가치 제안 / 타깃 고객 / 확장 로드맵 보강 -->

### Azure DevOps
- **Organization**: `https://dev.azure.com/Security365`
- **Project**: `EnforcementDecisionOrchestrator`
- **Area Path**: TODO 확인
- **현재 Iteration**: `I17 - 템플릿 매핑 기반 Iframe 연동 구현` (진행중, 2026.03.23 ~ 2026.04.17)
- **PAT**: 환경변수 `AZURE_DEVOPS_PAT` (5개 제품 공통 revoke 대기 — 2026-04-21)

### 저장소 구성
| 경로 | 역할 | 기술 스택 |
|---|---|---|
| `edo/` | 백엔드 | TODO 확인 (Java/Spring 추정) |
| `edo-fe/` | 프론트엔드 | TODO 확인 |
| `edo_docs/` | 제품 문서 사이트 | Docusaurus |
| `EDO_테스트케이스.xlsx` | 테스트 케이스 시트 | — |

<!-- TODO: edo / edo-fe 기술 스택 확정 -->

### 주요 기능 카테고리
- **조건부 정책** — 등록/조회, JSON validation(문법·구조·스키마·비즈니스 로직)
- **집행 정책** — 정책 결과(Result) 필드 정의, 노드 연결 구조
- **집행 워크플로우** — iframe 기반 UI, 서비스 페이지 내 매핑·템플릿 관리, 부모창-iframe 데이터 연동
- **템플릿 관리** — 생성·조회·수정·복사
- **정책-템플릿 매핑** — 트리거별 매핑, 우선순위 기반 해석, 전사 공통 매핑 + 개별 매핑 구분
- **서비스 연동(Integration)** — SHIELD Drive, SHIELD Gate 등 타 서비스와의 정책 구조 연동

### 문서 구조 (Docusaurus, 단일 사이트, Docmost sync 없음)

EDO는 **영문 폴더 + 번호 접두** 컨벤션:

```
edo_docs/docs/06.internal/
├─ 01.planning/
│  ├─ 01.Integration/
│  ├─ 01.release_note.md      ← 릴리즈 노트 (단일 파일, 이터레이션 역순 누적)
│  ├─ 02.roadmap.md
│  ├─ 02.ztcap/
│  ├─ 03.UseCase/
│  ├─ 04.research/
│  ├─ 05.MoM/                 ← 이터레이션 미팅 회의록
│  ├─ 06.function/
│  └─ 07.product_proposal/
├─ 02.development/
└─ 03.deployment/
```

frontmatter는 `sidebar_position`, `title`, `tags`만. SDF와 달리 Docmost 관련 필드 없음.

### 최근 마일스톤
- **I17 - 템플릿 매핑 기반 Iframe 연동 구현** (진행중, 2026.03.23 ~ 2026.04.17)
  - 조건부 정책 JSON validation / 집행 워크플로우 Iframe / 템플릿 관리 / 정책-템플릿 매핑
- **I16 - 조건부 정책 UI 최신화** (완료, 2026.03.09 ~ 2026.03.20)
- **I15 - 조건부정책 UI 및 사용성 개선** (완료, 2025.12.31 ~ 2026.01.27)

<!-- 이 섹션은 이터레이션 커맨드로 자동 갱신하는 구조로 전환 -->

### 주요 용어 (초안)
| 용어 | 설명 |
|---|---|
| **조건부 정책** | 특정 조건에 따라 집행될 정책 |
| **집행 정책** | 실제 동작을 정의하는 정책. 결과(Result) 필드 포함 |
| **집행 워크플로우** | 조건부 + 집행 정책을 조합해 실행되는 워크플로우 |
| **템플릿** | 재사용 가능한 정책 구성 단위 |
| **매핑** | 트리거/이벤트 ↔ 템플릿 연결 구조. 전사 공통 + 정책별 개별 |
| **Integration** | 타 서비스와 EDO 연동 구조. 서비스 정책 구조와의 이중 관리 이슈가 존재 (MoM `01.edo_service_linking.md` 참조) |

<!-- TODO: EDO 용어 보강 — Trigger, Action, Result 등 구체 정의 -->

---

## 문서 출력 경로 (EDO)

- **이터레이션별 릴리즈노트**: `edo_docs/docs/06.internal/01.planning/01.release_note.md`
  - **단일 파일**. `### ◈ I{n} - {제목}({상태})` 섹션을 역순 누적
  - 월요일 루틴 갱신 대상
- **이터레이션 미팅 회의록**: `edo_docs/docs/06.internal/01.planning/05.MoM/`
  - 미팅 단위 파일. 번호 접두 + 주제명 (예: `01.edo_service_linking.md`)
- **로드맵**: `edo_docs/docs/06.internal/01.planning/02.roadmap.md`
- **유스케이스**: `03.UseCase/`, **리서치**: `04.research/`, **기능 스펙**: `06.function/`

## 참고 파일
- `edo_docs/docs/06.internal/01.planning/05.MoM/01.edo_service_linking.md` — EDO 서비스 연동 구조 결정 논의
- `EDO_테스트케이스.xlsx` — 테스트 케이스 시트

## 보안 주의
- 기존 `.mcp.json`에 5개 제품 공통 PAT 평문 노출 상태 (2026-04-21 확인)
- 이번 세팅에서 `${env:AZURE_DEVOPS_PAT}` 참조로 전환. PAT revoke/재발급 후 환경변수에 새 값 주입하면 자동 복구
