---
name: docmost_edo
description: EDO Docmost 문서 구조 전체 맵, 페이지 ID, API 접근 방법 및 업데이트 스크립트 사용법
type: reference
originSessionId: 7da8aaa5-d46f-4037-af0d-5f0df72435e2
---
# EDO Docmost 문서 구조 및 API 접근

**최종 업데이트:** 2026-04-23  
**총 페이지 수:** 280개

---

## API 접근 정보

- **URL**: `https://docmost.softcamp.co.kr`
- **인증 방식**: Cookie (`authToken=<JWT>`)
- **EDO 스페이스 ID**: `019d2271-792a-7d5c-817d-5e0b4754bf68`
- **토큰 위치**: `~/.claude.json` → mcpServers.docmost.env.DOCMOST_AUTH_TOKEN

### 페이지 목록 조회 (API)
```bash
curl -s -X POST "https://docmost.softcamp.co.kr/api/pages/sidebar-pages" \
  -H "Content-Type: application/json" \
  -H "Cookie: authToken=$TOKEN" \
  -d '{"spaceId":"019d2271-792a-7d5c-817d-5e0b4754bf68"}'
```

### 페이지 내용 export (마크다운)
```bash
curl -s -X POST "https://docmost.softcamp.co.kr/api/pages/export" \
  -H "Content-Type: application/json" \
  -H "Cookie: authToken=$TOKEN" \
  -d '{"pageId":"<ID>","format":"markdown"}' -o output.zip
# unzip output.zip → *.md 파일
```

### 페이지 내용 업데이트 (WebSocket, 스크립트 사용)
```bash
cd C:/Users/Security365/source/repos/mcp-docmost
node update_page.mjs "<pageId>" "<content.md 경로>"
```
- 스크립트 위치: `C:/Users/Security365/source/repos/mcp-docmost/update_page.mjs`
- 내부적으로 Hocuspocus WebSocket 사용 (단순 REST가 아님)
- 성공 시: `{ success: true, nodesInserted: N }` 출력

### 스페이스 전체 export
```bash
curl -s -X POST "https://docmost.softcamp.co.kr/api/spaces/export" \
  -H "Content-Type: application/json" \
  -H "Cookie: authToken=$TOKEN" \
  -d '{"spaceId":"019d2271-792a-7d5c-817d-5e0b4754bf68","format":"markdown"}' \
  -o /tmp/edo_export.zip
cd /tmp && unzip -o edo_export.zip -d edo_export
# /tmp/edo_export/docmost-metadata.json → 전체 페이지 ID 맵
```

---

## 주요 페이지 ID (기획 작업 관련)

### 가이드 (관리자용)
| 경로 | 페이지 ID |
|---|---|
| 가이드 | `019d9b68-3d1a-76e9-97a5-b13dbefe2af5` |
| 가이드/관리자 가이드 | `019d9b68-3d1a-76e9-97a5-b63fadd6672d` |
| 가이드/관리자 가이드/조건부 정책 관리 | `019d9b68-3d18-779b-80f1-b371973bf8a4` |
| 가이드/관리자 가이드/집행 워크플로우 관리 | `019d9b68-3d18-779b-80f1-b6080f8b326a` |
| 가이드/관리자 가이드/서비스 연동 관리 | `019d9b68-3d18-779b-80f1-ac15433bd71a` |

### 내부 문서 > 기획
| 경로 | 페이지 ID |
|---|---|
| 내부 문서/기획 | `019d9b68-3d1a-76e9-97a5-bd82f52ca4f1` |
| 내부 문서/기획/릴리즈 노트 | `019d9b68-3d19-751d-9c97-0c7a89bf34b3` |
| 내부 문서/기획/기능 정의 | `019d9b68-3d1a-76e9-97a5-db4581294bee` |
| 내부 문서/기획/기능 정의/1. 템플릿 저장/배포/원복 | `019d9b68-3d19-751d-9c97-55ef8197b16f` |
| 내부 문서/기획/기능 정의/2. 서비스 연동 집행 정책 흐름 | `019d9b68-3d19-751d-9c97-5aceb125dddd` |
| 내부 문서/기획/유스케이스 | `019d9b68-3d1a-76e9-97a5-cc8acba7cefa` |
| 내부 문서/기획/회의록 | `019d9b68-3d1a-76e9-97a5-d42546f3df05` |
| 내부 문서/기획/조건부 정책 | `019d9b68-3d1a-76e9-97a5-c9e6e77b4430` |
| 내부 문서/기획/2026 로드맵 | `019d9b68-3d19-751d-9c97-11ab5c013ed9` |

### 제품 소개 / 지식 베이스
| 경로 | 페이지 ID |
|---|---|
| 제품 소개 | `019d9b68-3d1a-76e9-97a5-a4eed6b0c9f9` |
| 제품 소개/주요기능 | `019d9b68-3d18-779b-80f1-9b85142235ce` |
| 제품 소개/용어집 | `019d9b68-3d18-779b-80f1-9fd279335ac4` |
| 지식 베이스/FAQ | `019d9b68-3d18-779b-80f1-a4b61063d340` |
| 지식 베이스/용어집 | `019d9b68-3d18-779b-80f1-aa7fe2b2fb8d` |

---

## 전체 문서 트리 구조 (상위 레벨)

```
published/
├── EDO란?
├── 가이드/
│   └── 관리자 가이드/
│       ├── 조건부 정책 관리        ← 자주 수정
│       ├── 집행 워크플로우 관리    ← 자주 수정
│       └── 서비스 연동 관리
├── 내부 문서/
│   ├── 기획/
│   │   ├── 릴리즈 노트
│   │   ├── 기능 정의/
│   │   ├── 유스케이스/
│   │   ├── 회의록/
│   │   ├── 조건부 정책/
│   │   ├── Integration/
│   │   ├── 리서치/
│   │   ├── 2026 로드맵
│   │   └── EDO 고객 제안 (대외용 초안)/
│   └── 개발/
│       ├── Intergration 명세/
│       ├── Rest API 명세서/
│       ├── 릴리즈노트/
│       ├── 설정/
│       └── 집행 정책/
├── 제품 사양서/
├── 제품 소개/
│   ├── 주요기능
│   └── 용어집
└── 지식 베이스/
    ├── FAQ
    └── 용어집
```

---

## 페이지 ID 빠른 조회 방법

특정 페이지 ID를 모를 때:
```bash
# 스페이스 전체 export 후 메타데이터에서 검색
TOKEN="..."
curl -s -X POST "https://docmost.softcamp.co.kr/api/spaces/export" \
  -H "Cookie: authToken=$TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"spaceId":"019d2271-792a-7d5c-817d-5e0b4754bf68","format":"markdown"}' \
  -o /tmp/edo_export.zip && \
unzip -o /tmp/edo_export.zip docmost-metadata.json -d /tmp/edo_export && \
python3 -c "
import json,urllib.parse
data=json.load(open('/tmp/edo_export/docmost-metadata.json',encoding='utf-8'))
for path,info in data['pages'].items():
    decoded=urllib.parse.unquote(path)
    if '검색어' in decoded:
        print(info['pageId'], '|', decoded)
"
```
