# awesome-free-apps 전수조사 분석 및 활용 전략 (한국어 정리)

> 이 문서는 Claude Code 세션에서 `awesome-free-apps` 저장소를 전수조사하고,
> 활용 방안 · 기술 스택 · 수익화 전략까지 논의한 내용을 정리한 기록입니다.
>
> - 작성일: 2026-09-22
> - 대상 저장소(내 포크): https://github.com/bmshin94/awesome-free-apps
> - 원본(업스트림): https://github.com/Axorax/awesome-free-apps
> - 원본 커뮤니티: https://discord.com/invite/nKUFghjXQu
> - 원본 후원: https://patreon.com/axorax

---

## 목차

- [1. 프로젝트 정체](#1-프로젝트-정체)
- [2. 실측 통계](#2-실측-통계)
- [3. 파일/폴더 전수조사](#3-파일폴더-전수조사)
- [4. 동작 원리](#4-동작-원리)
- [5. 설치 및 사용법](#5-설치-및-사용법)
- [6. 플러그인 / 스킬 / MCP 구분](#6-플러그인--스킬--mcp-구분)
- [7. API 토큰 필요 여부](#7-api-토큰-필요-여부)
- [8. 왜 GitHub에서 유명한가](#8-왜-github에서-유명한가)
- [9. 로컬 에이전트 구축 활용법](#9-로컬-에이전트-구축-활용법)
- [10. React / PHP로 재구현하기](#10-react--php로-재구현하기)
- [11. 라이선스 제약 (중요)](#11-라이선스-제약-중요)
- [12. 수익화 아이디어 7가지](#12-수익화-아이디어-7가지)
- [13. 실행 로드맵](#13-실행-로드맵)
- [14. 발견된 기술적 주의사항](#14-발견된-기술적-주의사항)

---

## 1. 프로젝트 정체

**한 줄 요약**: "무료 앱 추천 리스트" 자체가 제품인 프로젝트. 코드가 아니라 `README.md`라는
문서가 주인공이고, `index.js`는 그 문서를 자동 가공하는 보조 스크립트다.

- 분류: **awesome-list** (GitHub 큐레이션 문서 저장소 문화)
- GitHub 토픽: `android`, `awesome`, `awesome-list`, `free-apps`, `ios`, `linux`, `macos`, `microsoft`, `mobile`, `windows`
- 원본 지표(2026-09 기준): ⭐ **7,718** / 🍴 **491** / 열린 이슈 45 / 생성 2024-10-24 / 언어 JavaScript
- 내 포크 커밋 수: 210

### 용도

| 상황 | 활용 |
|---|---|
| 무료 앱 탐색 | 카테고리에서 찾고 ⭐ 붙은 것 우선 검토 |
| 새 PC 세팅 | 필수 무료 앱 쇼핑 리스트 |
| 유료 앱 대체 | 🟢 오픈소스만 필터링 |
| GitHub 기여 연습 | 앱 1줄 추가로 7.7k 스타 저장소 컨트리뷰터 |

---

## 2. 실측 통계

| 항목 | 수치 |
|---|---|
| `README.md` (데스크톱) | 851줄 / 앱 **617개** / 링크 773개 / 82,664자 / 10,452단어 |
| `MOBILE.md` (모바일) | 414줄 / 앱 **198개** / 링크 184개 / 27,527자 / 3,686단어 |
| 대분류(`##`) | 24개 |
| 소분류(`###`) | 35개 |
| 🪟 Windows | 472 |
| 🍎 macOS | 430 |
| 🐧 Linux | 312 |
| 🟢 오픈소스 | 304 |
| ⭐ 강력추천 | 52 |
| 🤖 Android (모바일) | 146 |
| 🍎 iOS (모바일) | 118 |
| **총 앱 수** | **815개** |
| npm 의존성 | **0개** (`package.json` 자체가 없음) |
| 테스트 | 4개, 전부 통과 (`node --test tests/index.test.js`) |
| Node 버전 | 22 권장 (Actions 기준), 최소 18+ (`AbortSignal.timeout`) |

---

## 3. 파일/폴더 전수조사

### 원본 데이터 (사람이 직접 수정)

| 파일 | 설명 |
|---|---|
| `README.md` | PC용 앱 617개. 상단에 아이콘 범례표 + 자동 생성 목차 |
| `MOBILE.md` | 모바일 앱 198개. **주의: 여기서 🍎는 macOS가 아니라 iOS** |
| `archived.md` | 탈락 앱 목록. 이유 4종 분류: `Subpar`(수준 미달) / `Unmaintained`(개발 중단) / `Deferred`(검토 보류) / `Sunset`(프로젝트 종료) |

### 자동 생성물 (수정 금지)

`filter/` — 9개 파일, 총 약 300KB. README/MOBILE에서 **이모지만 보고 걸러낸 파생 리스트**.

| 파일 | 원본 | 기준 이모지 |
|---|---|---|
| `windows-only.md` | README | 🪟 |
| `macOS-only.md` | README | 🍎 |
| `linux-only.md` | README | 🐧 |
| `open-source-only.md` | README | 🟢 |
| `recommended-only.md` | README | ⭐ |
| `android-only.md` | MOBILE | 🤖 |
| `iOS-only.md` | MOBILE | 🍎 |
| `open-source-mobile-only.md` | MOBILE | 🟢 |
| `recommended-mobile-only.md` | MOBILE | ⭐ |

### 엔진 — `index.js` (322줄, 순수 Node 내장 모듈만)

| 함수 | 역할 |
|---|---|
| `create(file, name, emoji)` | 앞 20줄(헤더)은 보존, 21줄부터 대상 이모지 없는 줄 제거 → `filter/*.md` 생성. 상대 경로도 보정(`./filter/` 제거, `./` → `../`) |
| `categorize()` | `create()`를 9번 호출 |
| `generateToc(path)` | `##`~`######` 제목 수집 → 들여쓰기 목차 생성, 앵커 슬러그화 |
| `findAF(file, name, msg, suffix)` | `<!-- AF-TOC ... -->` ~ `<!-- AF-END -->` 사이 치환 + 생성 시각 기록 |
| `createToC()` | README/MOBILE 목차 갱신 |
| `format(file)` | 링크 끝 트레일링 슬래시 제거 (위험한 규칙은 주석 처리돼 있음) |
| `getLinks(file)` | 정규식으로 마크다운 링크 URL 추출 |
| `countLinks(file)` | 링크 개수 출력 |
| `testUrl(url, opts)` | HEAD → 실패 시 GET 재시도, 리다이렉트 추적, 10초 타임아웃 |
| `mapWithConcurrency(items, n, worker)` | 워커 풀 직접 구현 (기본 동시 12) |
| `testLinksReachable(file, opts)` | 중복 URL 제거 후 죽은 링크 리포트 |
| `testLinksInAllFiles()` | README + MOBILE 전체 링크 감사 |
| `analyze(file)` | 단어/글자/링크 수 통계 |
| `fastGit(msg)` | `git add -A` → `commit` → `push` 원샷 |
| `runAll()` | 링크 카운트 → 목차 → 필터 → 링크 카운트 |

`module.exports`로 공개된 함수: `getLinks`, `mapWithConcurrency`, `testLinksInAllFiles`, `testLinksReachable`, `testUrl`

### 테스트 — `tests/index.test.js`

`node:test` 기반 4개 테스트. 의존성 주입(`fetchImpl`, `testUrlImpl`)과 로컬 `node:http` 서버를
띄워 네트워크 없이 자기완결적으로 검증한다.

1. `getLinks`가 HTTP 링크만 추출하는지 (로컬 이미지 경로 제외)
2. `testUrl`이 리다이렉트 추적 / 404 거부 / HEAD 405 시 GET 재시도 하는지
3. `mapWithConcurrency`가 동시 실행 상한을 넘지 않는지
4. `testLinksReachable`이 중복 제거 + 실패 리포트 하는지

### 자동화 — `.github/workflows/` 3개

| 워크플로 | 트리거 | 동작 |
|---|---|---|
| `Update main file.yml` | `workflow_dispatch` (수동) | `node index.js --all` → 봇 계정으로 커밋 & 푸시. 문서상 **주 1회 실행 권장** |
| `renew-categories.yml` | `workflow_dispatch` (수동) | `node index.js --categorize` → 필터만 재생성 후 푸시 |
| `link count.yml` (job명 Link audit) | PR (`index.js`, 테스트, 워크플로 변경 시) + 수동 | PR이면 테스트만. **전체 링크 감사는 수동 실행 때만** (레이트리밋 회피 목적) |

### 커뮤니티 운영 문서

| 파일 | 내용 |
|---|---|
| `contributing.md` | 축약 기여 규칙 |
| `full-guide.md` | 상세 규칙 + 추천(⭐) 부여 기준 + 메인테이너 가이드 |
| `how-to-make-a-pr.md` | 초보자용 스크린샷/영상 PR 가이드 |
| `code-of-conduct.md` | Contributor Covenant |
| `.github/pull_request_template.md` | 체크박스 30여 개 (카테고리, 플랫폼, 오픈소스, archived 확인 여부) |
| `.github/FUNDING.yml` | `patreon: axorax` |
| `logo.svg`, `.github/afa.png` | 로고 |
| `LICENSE` | **CC BY-NC-SA 4.0** (11장 참조) |
| `CLAUDE.md` | 내가 추가한 Claude Code 프로젝트 지침 (페르소나 설정) |

---

## 4. 동작 원리

### 이모지가 곧 데이터베이스 스키마

앱 한 줄의 구조:

```
- [Audacity](https://audacityteam.org/download) - Audio editor for recording and editing sounds. 🪟 🍎 🐧 🟢 ⭐
  └─이름─┘ └──────────URL───────────┘   └──────────설명──────────┘  └플랫폼┘ └OSS┘ └추천┘
```

| 부분 | 대응 DB 필드 |
|---|---|
| `[이름]` | `name` |
| `(URL)` | `url` |
| `- 설명.` | `description` |
| 🪟 🍎 🐧 🤖 | `platforms[]` |
| 🟢 | `open_source` (링크면 `repo_url`) |
| ⭐ | `recommended` |

DB 없이 텍스트 + 이모지로 3개 필드를 구현한 구조. 기여 규칙이 곧 스키마 강제 장치다.

### Single Source of Truth 파이프라인

```
사람이 README.md에 앱 1줄 추가 (PR)
        │
        ├─ 메인테이너 리뷰 & 머지
        │
        └─ Actions "Update main file" 수동 실행
                 │
                 ├─ node index.js --all
                 │     ├─ createToC()    : 목차 마커 사이 치환
                 │     └─ categorize()   : filter/ 9개 재생성
                 │
                 └─ github-actions[bot] 커밋 & 푸시
```

### 필터 생성 로직

```
README.md 한 줄씩 읽기
 ├ 1~20줄  → 헤더(로고·범례표)이므로 그대로 복사
 └ 21줄부터
     ├ 이모지가 하나도 없다     → 복사 (제목/빈 줄/카테고리명)
     ├ 대상 이모지 포함         → 복사 ✅
     └ 다른 이모지만 있다       → 버림 ❌
```

### 링크 검사 로직

- **HEAD** 요청 먼저 (본문 미수신 → 빠름)
- HEAD 거부 서버(405 등)면 **GET 재시도**
- **동시 12개** 워커 풀 (`Promise.all` 전체 동시 발사를 피해 레이트리밋/차단 회피)
- 10초 타임아웃 (`AbortSignal.timeout`)

### 기여 규칙 (스키마 유지 목적)

- 앱은 해당 카테고리 **맨 아래**에만 추가 (순서 변경 금지)
- 목차, `filter/` 폴더 수정 금지 (봇 담당)
- 설명은 `A`, `An`, `The`로 시작 금지 → `A lightweight app` ❌ / `Lightweight app` ✅
- 설명은 마침표 `.`로 끝내기 (`!`, `?` 금지)
- 카테고리명 반복 같은 무정보 설명 금지
- 오픈소스면 🟢를 저장소 링크로: `[🟢](https://github.com/...)`
- 커밋 메시지: `Add: 이름` / `Update: 이름` / `Remove: 이름` (`Add (PC):`, `Add (MOBILE):` 형태도 허용)
- 코드 기여는 `Feat:` / `Update:`, **단순 리팩터링만은 반영 안 됨**

---

## 5. 설치 및 사용법

### 설치

의존성 0개. `npm install` 불필요.

```bash
git clone https://github.com/bmshin94/awesome-free-apps.git
cd awesome-free-apps
node -v   # v22 권장 (최소 18+)
```

### 읽기용 (대부분의 사용자)

| 상황 | 파일 |
|---|---|
| 전체 데스크톱 | `README.md` |
| 전체 모바일 | `MOBILE.md` |
| Windows만 | `filter/windows-only.md` |
| macOS만 | `filter/macOS-only.md` |
| Linux만 | `filter/linux-only.md` |
| 오픈소스만 | `filter/open-source-only.md` |
| **엄선 52개** | `filter/recommended-only.md` ← 시간 없으면 여기부터 |
| Android / iOS | `filter/android-only.md` / `filter/iOS-only.md` |

### CLI

```bash
node index.js                          # 도움말
node index.js --analyze                # 단어/글자/링크 통계
node index.js --links                  # 링크 개수
node index.js --toc                    # 목차 갱신
node index.js --categorize             # filter/ 9개 재생성
node index.js --format                 # 링크 트레일링 슬래시 정리
node index.js --all                    # 위 전부 (실무에선 이것만 사용)
node index.js --test-links             # 957개 링크 생존 검사 (수 분 소요)
node index.js --fastgit "Add: 앱이름"   # add + commit + push 원샷
node --test tests/index.test.js        # 테스트 (4/4 통과 확인)
```

### 앱 추가 절차

1. 포크 → 브랜치 생성
2. `README.md`(PC) 또는 `MOBILE.md`(모바일)의 해당 카테고리 **맨 아래**에 1줄 추가
3. 형식: `- [이름](https://사이트) - 설명. 🪟 🍎 [🟢](https://github.com/...)`
4. 커밋: `Add: 앱이름`
5. PR 생성 → 템플릿 체크박스 작성
6. 목차와 `filter/`는 절대 수정하지 않는다

---

## 6. 플러그인 / 스킬 / MCP 구분

**결론: 세 가지 모두 아니다.** 정체는 "awesome-list 형식 큐레이션 문서 저장소 + 자체 Node 유틸 CLI".

| 구분 | 정체 | 해당 여부 |
|---|---|---|
| **MCP 서버** | AI가 외부 도구/데이터에 접근하는 표준 프로토콜(JSON-RPC로 툴·리소스 노출) | ❌ 서버 코드·설정 전무 |
| **Skill** | `SKILL.md` + 프론트매터로 특정 작업 절차를 가르치는 패키지 | ❌ `.claude/skills/` 없음 |
| **플러그인** | 명령어·에이전트·훅·스킬을 묶어 배포하는 Claude Code 확장 | ❌ `plugin.json`, 마켓플레이스 설정 없음 |
| **awesome-list** | GitHub 주제별 큐레이션 마크다운 저장소 문화 | ✅ **이것** |

AI와 연결된 유일한 요소는 내가 추가한 `CLAUDE.md`. 이는 Claude Code가 해당 디렉터리에서
세션 시작 시 자동으로 읽는 **프로젝트 지침(메모리) 파일**이며, 플러그인/스킬/MCP와는 다른 범주다.

### 세 가지로 확장 가능

- **Skill**: `.claude/skills/find-free-app/SKILL.md` — "무료 앱 추천해줘" 시 README 파싱해 응답
- **MCP 서버**: `search_apps(category, platform, openSource)` 툴 노출 → Claude Desktop/Cursor 등에서 815개 앱 검색 (Node 약 100줄)
- **플러그인**: 위 둘 + `/find-app` 슬래시 커맨드를 묶어 배포

---

## 7. API 토큰 필요 여부

**필요 없다.** 코드 전체에 `process.env`로 키를 읽는 곳이 한 곳도 없다.

| 기능 | 토큰 |
|---|---|
| 리스트 읽기 | ❌ |
| `--toc`, `--categorize`, `--format`, `--analyze`, `--links` | ❌ 로컬 파일 작업 |
| `--test-links` | ❌ 익명 HTTP 요청 |
| 테스트 | ❌ 로컬 http 서버로 자기완결 |
| Actions 푸시 | ⚠️ `GITHUB_TOKEN`을 사용하나 Actions가 자동 주입 (설정 불필요) |
| `--fastgit` | ⚠️ 일반 `git push`이므로 기존 git 인증(SSH키/PAT)만 필요 |

### 현실적 주의사항

`--test-links`는 957개 링크를 익명으로 요청한다. 그중 GitHub 링크가 많은데
**비인증 GitHub API/페이지 요청은 IP당 시간당 제한**이 있다. 그래서 Actions에서도
PR마다 돌리지 않고 수동 실행 때만 전체 감사를 수행한다.
로컬에서 자주 돌릴 계획이면 GitHub 도메인에만 토큰 헤더를 붙이는 개선을 권한다.

---

## 8. 왜 GitHub에서 유명한가

1. **주제의 범용성** — "무료 앱"은 컴퓨터 쓰는 모든 사람의 관심사. 특정 언어/프레임워크 리스트와 잠재 독자 규모가 다르다
2. **awesome-list 생태계 버프** — `awesome`, `awesome-list` 토픽으로 자동 노출 + 품질에 대한 사전 신뢰
3. **기여 허들이 극단적으로 낮음** — 코드 지식 불필요, 브라우저에서 1줄 추가로 PR 완성. 스크린샷·영상 가이드까지 제공 → 컨트리뷰터 이력을 원하는 사람들이 유입 (491 포크의 실체이자 핵심 성장 엔진)
4. **필터가 킬러 기능** — 통짜 리스트가 아니라 OS별/오픈소스별 9분할 제공
5. **관리가 살아있음** — 2026년 9월까지 지속 커밋. `archived.md`로 탈락 이유까지 공개해 신뢰도 확보
6. **자동화 = 확장성** — 목차/필터를 수동 관리하면 앱 수백 개에서 메인테이너 번아웃으로 프로젝트 사망. 봇이 처리하므로 815개도 유지 가능
7. **기여자가 홍보 채널** — 자기 앱이 등재된 개발자가 SNS 확산. `⭐ Recommended` 뱃지가 명예 인센티브
8. **SEO** — "best free apps" 류 검색어에서 GitHub 도메인 파워로 상위 노출
9. **수익 전환 설계** — Patreon 배너를 README 최상단 `[!IMPORTANT]` 박스에 배치

**교훈**: 기술이 아니라 **주제 선택 + 기여 허들 낮추기 + 자동화** 3박자가 스타를 만들었다.

---

## 9. 로컬 에이전트 구축 활용법

"직접 쓰는 도구"로는 아니지만 **재료와 설계 패턴**으로는 유용하다.

### 도움되는 것

**1) 에이전트용 도구 카탈로그 데이터**

```json
{
  "name": "Audacity",
  "url": "https://audacityteam.org/download",
  "desc": "Audio editor for recording and editing sounds.",
  "platforms": ["windows", "macos", "linux"],
  "openSource": true,
  "repo": null,
  "recommended": true,
  "category": "Audio",
  "sub": "Audio Recording"
}
```

815개 × (이름+설명+플랫폼+카테고리) ≈ 3만 토큰. **전체를 컨텍스트에 직접 넣어도 되는 크기**라
벡터DB/RAG 없이 프롬프트 스터핑만으로 충분하다.

**2) MCP 서버 첫 실습 소재**

DB·인증이 필요 없고 데이터가 이미 정제돼 있다.

```
search_apps(query, platform?, openSource?, recommended?) → 앱 목록
get_category(name)                                       → 카테고리 전체
suggest_alternative("Photoshop")                         → 무료 대체품
```

**3) "판단은 LLM, 반복은 코드" 분리 패턴 (가장 중요)**

```
에이전트 : README.md에 1줄 추가만  (판단이 필요한 창의적 작업)
스크립트 : 목차 + 필터 9개 생성     (결정적·기계적 작업)
```

에이전트에게 파생물 9개까지 시키면 실수하고 토큰도 낭비한다. 이 분리가 로컬 에이전트
설계의 핵심 원칙이며, 이 저장소가 교과서적 예제다.

**4) `index.js` 함수 재활용** — `getLinks`, `testUrl`, `mapWithConcurrency`가 이미 export돼 있고
의존성이 0이라 그대로 복사해 에이전트 툴로 쓸 수 있다.
- `check_links(file)` — 문서 작성 후 죽은 링크 자동 검증
- `mapWithConcurrency` — 에이전트의 병렬 API 호출 레이트리밋 제어용 워커 풀

**5) `CLAUDE.md` + Actions 루프** — 지침으로 성격·규칙 고정 + 스케줄 실행. 로컬 에이전트의
"스케줄러 + 실행환경" 미니 버전.

### 기대하면 안 되는 것

- 실행 가능한 에이전트 코드 (LLM 호출 코드 0줄)
- 벡터DB / 임베딩 / RAG 파이프라인
- JSON API 엔드포인트 (직접 파싱 필요)
- 설치 명령 정보 — 링크가 앱 **홈페이지**라서 `winget`/`brew` ID는 없다. 설치 자동화하려면 매핑 테이블을 직접 만들어야 한다

### 로드맵

| 단계 | 내용 | 예상 소요 |
|---|---|---|
| 1 | README/MOBILE 파서 → `apps.json` (815개) | 1시간 |
| 2 | `apps.json` 기반 MCP 서버 (search/filter) | 3시간 |
| 3 | `winget`/`brew` ID 매핑 추가 → 설치까지 수행하는 에이전트 | 1일 |
| 4 | 링크 검증 + 신규 앱 자동 발굴 에이전트 (cron) | 주말 |

3단계에 도달하면 "새 노트북 세팅해줘" → 앱 20개 자동 설치가 가능하다.

---

## 10. React / PHP로 재구현하기

### 0단계 공통: 마크다운 → JSON 파서

```javascript
// parse.js — 815개 앱을 JSON으로
const fs = require('fs');

function parse(file, mobile = false) {
  const apps = [];
  let category = '', sub = '';

  for (const line of fs.readFileSync(file, 'utf8').split('\n')) {
    if (line.startsWith('## '))  { category = line.slice(3).trim(); sub = ''; continue; }
    if (line.startsWith('### ')) { sub = line.slice(4).trim(); continue; }
    if (!line.startsWith('- [')) continue;

    const m = line.match(/^- \[(.+?)\]\((.+?)\)\s*-\s*(.*)$/);
    if (!m) continue;
    const [, name, url, rest] = m;

    // 🟢는 [🟢](repo) 형태일 수 있다
    const repo = rest.match(/\[🟢\]\((.+?)\)/)?.[1] ?? (rest.includes('🟢') ? url : null);

    apps.push({
      name, url, category, sub,
      desc: rest.replace(/\[?[🪟🍎🐧🟢⭐🤖]\]?(\(.+?\))?/g, '').trim(),
      platforms: [
        rest.includes('🪟') && 'windows',
        rest.includes('🐧') && 'linux',
        rest.includes('🤖') && 'android',
        rest.includes('🍎') && (mobile ? 'ios' : 'macos'), // 파일별 의미 상이
      ].filter(Boolean),
      openSource: rest.includes('🟢'),
      repo,
      recommended: rest.includes('⭐'),
    });
  }
  return apps;
}

fs.writeFileSync('apps.json', JSON.stringify(
  [...parse('README.md'), ...parse('MOBILE.md', true)], null, 2));
```

**파싱 함정**
- 🍎가 README에서는 macOS, MOBILE에서는 iOS
- `🐧[🟢](...)` 처럼 공백 없이 붙은 사례 존재 (TuxGuitar) → 엄격한 정규식보다 `includes`가 안전
- `create()`가 헤더를 "앞 20줄"로 하드코딩하므로, 헤더 줄 수가 바뀌면 필터가 깨진다

### A. React 버전

```
apps.json (약 300KB)
   ↓ Vite + React + TypeScript
검색바(Fuse.js 퍼지검색) + 플랫폼 토글 + 오픈소스/추천 필터
   ↓ Tailwind 카드 그리드
GitHub Pages / Vercel 무료 배포
```

- 815개면 서버/DB 불필요. JSON을 프론트에 로드해 클라이언트 필터링 → 검색 체감 0ms
- `Fuse.js`로 오타/부분 검색 대응
- Actions로 README 변경 시 자동 재빌드 → 완전 서버리스, 월 0원
- 카드 렌더링이 느려지면 `react-window` 가상 스크롤
- 예상 소요: 주말 2일

### B. PHP 버전

```php
<?php
$apps = json_decode(file_get_contents('apps.json'), true);
$q  = $_GET['q']  ?? '';
$os = $_GET['os'] ?? '';

$filtered = array_filter($apps, function ($a) use ($q, $os) {
    $okQ  = !$q  || stripos($a['name'] . ' ' . $a['desc'], $q) !== false;
    $okOs = !$os || in_array($os, $a['platforms'], true);
    return $okQ && $okOs;
});
```

- 제대로 하려면 Laravel + **SQLite** (815행이면 충분)
- 검색은 `FULLTEXT` 또는 Meilisearch
- **서버 렌더링 = SEO 우위** ← 수익화(12장)에 결정적
- Actions cron으로 파서 실행 후 DB 갱신

### 선택 가이드

| 목적 | 추천 | 이유 |
|---|---|---|
| 빠른 구축 + 포트폴리오 | React | 무료 배포, 개발 속도, UX |
| **SEO 트래픽 → 수익화** | PHP(Laravel) 또는 Next.js | 서버 렌더링이 검색에 유리 |
| 둘 다 | **Next.js** | React 문법 + SSR 동시 확보 |

수익화를 목표로 하면 **Next.js** 권장. PHP에 익숙하면 Laravel도 충분히 좋은 선택.

---

## 11. 라이선스 제약 (중요)

원본 `LICENSE`는 **Creative Commons BY-NC-SA 4.0**.

| 조항 | 의미 | 수익화 영향 |
|---|---|---|
| **BY** | 출처(Axorax) 표시 필수 | 표시하면 문제 없음 |
| **NC** | **비상업적 이용만 허용** | 🚨 광고·유료 서비스에 이 데이터를 그대로 쓰면 위반 |
| **SA** | 2차 저작물도 동일 라이선스 공개 | 🚨 독점 상품화 불가 |

### 합법 경로 3가지

1. **데이터 재구축 (가장 현실적)** — 앱 이름·URL 같은 **사실 정보 자체에는 저작권이 없다**.
   저작물은 목록의 구성과 설명 문장(표현)이다. 따라서 목록을 참고하되 **설명을 직접 다시 쓰고,
   분류를 재설계하고, 앱을 직접 검증**하면 독자 저작물이 된다. 한국어 서비스라면 어차피
   새로 써야 하므로 자연스럽게 해결된다.
2. **구조·노하우만 차용** — "이모지 태그 + 자동 필터 생성 + Actions 봇" 패턴은 아이디어이므로 자유롭게 사용 가능
3. **원작자 허락** — 이슈/디스코드로 상업 이용 허락 문의 (Patreon 후원과 함께 제안)

### 추가 리스크

- **설치 파일 재호스팅 금지** — 반드시 공식 사이트 링크. 재배포는 각 앱 라이선스 위반 + 멀웨어 유포 의심 소지
- **상표** — 앱 로고/이름은 상표. 비교·소개 목적은 공정 이용이나 자사 제품처럼 사용 불가
- 번들 설치기를 만들 때는 **각 앱의 재배포 조건** 확인 필수 (Ninite는 공식 서버에서 실시간 다운로드로 회피)

---

## 12. 수익화 아이디어 7가지

### 아이디어 1 — 한국어 무료앱 큐레이션 미디어 (1순위)

원본은 영어권. 한국어로 신뢰할 만한 무료 앱 큐레이션 사이트가 사실상 비어 있다.
검색 수요는 확실한데 공급 품질이 낮은 = 시장 기회.

```
freeapps.kr (예시)
├─ /win /mac /linux /android /ios
├─ /카테고리/영상편집        ← SEO 랜딩
├─ /대체품/포토샵            ← "포토샵 무료 대체" 검색 유입 (핵심)
├─ /추천/신규노트북세팅      ← 큐레이션 묶음
└─ /주간레터                 ← 이메일 자산 축적
```

| 채널 | 방식 | 월 예상 (MAU 3만) |
|---|---|---|
| Google AdSense | 배너 + 인피드 | 30~80만원 |
| 쿠팡파트너스 | 관련 하드웨어 제휴 | 20~50만원 |
| SW 제휴 | 무료 소개 후 유료 상위 버전 제휴 | 30~100만원 |
| 스폰서 슬롯 | 카테고리 상단 노출 판매 (광고 표기 필수) | 개당 20~50만원 |
| 뉴스레터 광고 | 구독 5천 이상 | 회당 30~50만원 |

기대치: 3개월 월 5~20만원 / 6개월 30~80만원 / 12개월 100~300만원.
성공 조건은 **직접 써본 후기 + 스크린샷** (AI 대량 생성 글은 구글 저품질 판정 대상).

### 아이디어 2 — 일괄 설치기 SaaS (Ninite 스타일)

```
① 웹에서 앱 체크박스 선택 (815개 카탈로그)
② "스크립트 생성"
③ 복붙: winget install -e Audacity.Audacity VideoLAN.VLC Mozilla.Firefox ...
   (macOS: brew install --cask vlc firefox ...)
④ 수 분 후 일괄 설치 완료
```

- 설치 파일을 직접 호스팅하지 않으므로 라이선스/멀웨어 리스크 0
- 난이도는 낮고, **앱 815개 ↔ winget/brew ID 매핑 테이블**이 진입장벽 겸 자산

| 티어 | 가격 | 기능 |
|---|---|---|
| Free | 0원 | 스크립트 생성, 앱 10개 제한 |
| Pro | 월 4,900원 | 무제한, 프로필 저장/공유, `.ps1`/`.sh` 다운로드, 업데이트 알림 |
| Team | 월 29,000원 | 사내 표준 프로필, 계정별 배포, 감사 로그 |
| Enterprise | 협의 | 온프레미스, Intune/Jamf 연동 |

확장 기능: 내 PC 스캔 후 "유료 → 무료 대체" 제안 / 신입 온보딩 원클릭 / 버전 고정.
**B2B 객단가가 10배**라 처음부터 팀 온보딩을 겨냥하는 편이 유리. MVP 2주 + 매핑 1~2주.

### 아이디어 3 — "유료 → 무료 대체품" 비교 사이트

"무료 앱 추천"보다 "포토샵 무료로 쓰는 법" 검색량이 압도적이고 전환율도 높다.

```
/alternatives/photoshop
├─ 유료 원본 정보 (월 2.4만원)
├─ 무료 대체품 3개 비교표 (GIMP / Krita / Photopea)
│   └─ 기능별 O/X, RAM 사용량, 학습곡선, 한글 지원
├─ "이럴 때는 유료가 낫습니다" 섹션  ← 신뢰의 핵심
└─ 유료 앱 할인 제휴 링크           ← 역설적 수익원
```

- SaaS 제휴(첫 결제의 20~30%, 일부는 반복 수수료) + 애드센스
- 유료 앱 200개 × "무료 대체" = 페이지 200개 롱테일
- 기능 비교표 데이터를 유료 API로 판매하는 파생 모델도 가능

### 아이디어 4 — 뉴스레터 + 스폰서십

플랫폼 알고리즘에 영향받지 않고 이메일 리스트는 온전한 자산이 된다.

```
매주 화요일 "이번 주 무료앱 3개"
├─ 신규 발굴 앱 1개 (직접 사용 후기 + 스크린샷)
├─ 숨은 명작 1개
├─ 유료 대체 팁 1개
└─ 스폰서 섹션 ([광고] 명시)
```

| 구독자 | 1회 단가 | 월(4회) |
|---|---|---|
| 1,000 | 10~15만원 | 40~60만원 |
| 5,000 | 30~50만원 | 120~200만원 |
| 10,000+ | 80~150만원 | 300~600만원 |

아이디어 1의 트래픽 → 구독 전환이 가장 효율적. 발송은 Stibee(국내, 무료 2천명) 또는
Beehiiv(영어권, 광고망 연결). **표시광고법상 광고 표기 필수.**

### 아이디어 5 — MCP 서버 / Claude 스킬 배포

직접 수익보다 **인지도 확보용 무기**로 활용.

```
무료 배포: 무료앱 검색 MCP 서버
  → Claude Desktop / Cursor 사용자 설치
  → 개발자 커뮤니티 화제 (MCP 생태계 초기라 노출 용이)
  → 아이디어 1·2 서비스 인지도 상승
  → 실제 수익은 본체에서 회수
```

수익 갈래
1. **MCP 서버 개발 대행** — 기업 커스텀 MCP 제작 건당 300~1,000만원 (현재 공급 부족)
2. **유료 MCP** — 기본 검색은 무료, 라이선스 검증/보안 스캔 등 부가 데이터는 유료 키
3. **강의/전자책** — "MCP 서버 만들기 실전" (이 프로젝트를 예제로)

MCP 생태계는 아직 초기이므로 타이밍이 중요하다.

### 아이디어 6 — B2B 소프트웨어 비용 절감 컨설팅

중소기업·스타트업은 SaaS 비용을 과다 지출하면서 사용 현황조차 파악하지 못하는 경우가 많다.

| 상품 | 내용 | 가격 |
|---|---|---|
| 무료 진단 | 설문 10개 → 절감 추정 리포트 (리드 확보) | 0원 |
| 기본 리포트 | 현황 분석 + 대체 SW 제안 + 마이그레이션 난이도 | 50~150만원 |
| 실행 컨설팅 | 전환 실행 + 교육 + 3개월 사후관리 | 300~1,000만원 |
| 구독 | 반년 재진단 + 신규 SW 모니터링 | 월 30~100만원 |

- "월 300만원 → 80만원" 제안은 CFO가 거절하기 어렵다
- 성과 기반 과금(절감액의 20%)이면 고객 리스크 0 → 계약률 상승
- 815개 카탈로그가 제안서 데이터베이스 역할
- 주의: 오픈소스는 라이선스만 무료이고 **운영 인건비는 발생**한다는 점(TCO)을 정직하게 밝혀야 신뢰가 생긴다. 첫 레퍼런스는 할인/무료로 확보

### 아이디어 7 — 구조 복제 (자산 공장화)

이 프로젝트의 진짜 가치는 데이터가 아니라 시스템이다.

```
[이모지 태그 마크다운] + [자동 파생 생성기] + [Actions 봇] + [엄격한 기여 규칙]
```

| 복제 후보 주제 | 수익화 |
|---|---|
| 무료 공공데이터 API 모음 | 개발자 유입 → SaaS |
| 한글 무료 폰트 (라이선스별) | 디자이너 트래픽, 폰트 제휴 |
| 무료 SaaS 티어 비교 | 제휴 수수료 |
| 개발자 무료 크레딧 / 학생 혜택 | 클라우드 제휴 |
| 무료 AI 도구 | 현재 수요 최고 |

첫 프로젝트는 3개월, 두 번째부터는 약 2주. 하나가 성공하면 나머지를 지탱하는 포트폴리오
전략이 되며, 월 매출 20~30배 수준에서 사이트 자체를 매각하는 출구도 존재한다.

---

## 13. 실행 로드맵

### 추천 조합

```
1순위  아이디어 1 + 3 결합 → 한국어 무료앱/대체품 사이트 (Next.js)
       SEO 자산 축적. 느리지만 확실. 초기 비용은 도메인비(연 2만원) 수준

2순위  아이디어 2 → 설치기 SaaS, 특히 B2B 팀 온보딩
       1순위 사이트가 유입 채널 역할. 시너지 우수

병행   아이디어 5 → MCP 서버 무료 배포
       개발자 인지도 + 기술 포트폴리오. 주말 1회로 가능
```

### 첫 30일

| 주 | 할 일 |
|---|---|
| 1주 | `parse.js` 작성 → `apps.json` 완성 / 도메인 구매 / 애드센스 사전 신청(승인 2~4주) |
| 2주 | Next.js 스켈레톤 + 검색·필터 배포(Vercel) / 상위 50개 앱 한국어 설명 직접 작성 |
| 3주 | "OO 무료 대체" 글 10개 / 서치콘솔 등록 / MCP 서버 공개 |
| 4주 | 커뮤니티 공유(클리앙·GeekNews·Reddit) / 뉴스레터 구독 폼 / 트래픽 분석 세팅 |

### 실패 요인 3가지

1. **AI로 815개 설명 자동 생성** → 저품질 판정, 색인 거부. **50개를 제대로** 쓰는 것이 800개 대충보다 낫다
2. **조급한 수익화** → 초기 광고 도배는 이탈을 유발. 트래픽 월 1만 이후 도입
3. **라이선스 무시** → NC 조항 위반으로 DMCA·광고 계정 정지 위험. **데이터 재구축이 1주차 최우선 과제**

---

## 14. 발견된 기술적 주의사항

| 위치 | 내용 | 영향 |
|---|---|---|
| `index.js` `fastGit()` | 커밋 메시지를 템플릿 문자열로 셸 명령에 직접 보간 (`execSync` + `git commit -m "..."`) | 커밋 메시지에 `"`, 백틱, `$()` 포함 시 셸 인젝션. 이 패턴을 서버 코드로 복사하지 말 것. `execFileSync('git', ['commit','-m',msg])`로 교체 권장 |
| `index.js` `create()` | 헤더를 "앞 20줄"로 하드코딩 | 헤더 줄 수가 바뀌면 필터 파일이 깨진다 |
| 🍎 이모지 | README=macOS, MOBILE=iOS로 의미가 다름 | 파서 작성 시 파일별 분기 필요 |
| 🟢 표기 | `🟢`와 `[🟢](repo)` 두 형태 혼용, 공백 없이 붙은 사례도 존재 | 엄격한 정규식보다 `includes` 기반 판정이 안전 |
| `runAll()` | `formatFiles()`가 주석 처리돼 있음 | `--all`은 실제로 format을 수행하지 않는다 |
| `format()` | `https://www.` 제거 규칙이 "링크가 깨진다"는 이유로 주석 처리 | 의도된 비활성화 |
| `--test-links` | 957개 링크를 익명 요청 | GitHub 링크 다수 → 레이트리밋. Actions에서 수동 실행으로만 제한한 이유 |
| `LICENSE` | CC BY-NC-SA 4.0 | 상업적 이용 제한 (11장 참조) |

---

## 참고 링크

- 내 포크: https://github.com/bmshin94/awesome-free-apps
- 원본 저장소: https://github.com/Axorax/awesome-free-apps
- 원본 기여 가이드: https://github.com/Axorax/awesome-free-apps/blob/main/full-guide.md
- 원본 메인테이너 모집 이슈: https://github.com/Axorax/awesome-free-apps/issues/28
- 원본 디스코드: https://discord.com/invite/nKUFghjXQu
- 원본 후원(Patreon): https://patreon.com/axorax
- 라이선스 전문: https://creativecommons.org/licenses/by-nc-sa/4.0/
