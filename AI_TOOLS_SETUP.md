# AI 도구 3종 설치 가이드 (내 컴퓨터용)

인스타그램에서 소개된 세 도구를 각 저장소 README 원문 기준으로 정리했습니다 (2026-11-26 확인).

| 도구 | 저장소 | 깃허브 별 | 라이선스 | 게시물 주장 확인 |
|---|---|---|---|---|
| chrome-devtools-mcp | [ChromeDevTools/chrome-devtools-mcp](https://github.com/ChromeDevTools/chrome-devtools-mcp) | 약 5만 9천 | Apache-2.0 | ✅ "별 5만 9천" 일치 |
| agent-desktop | [vercel-labs/agent-desktop](https://github.com/vercel-labs/agent-desktop) | 약 2만 7천 | Apache-2.0 | ✅ "2만 개 넘음" 일치 |
| Craft Anything | [storytold/craft-anything](https://github.com/storytold/craft-anything) | 약 4만 4천 | MIT / Apache-2.0 | ✅ 10월 31일 공개, "4만 2천" 이후 더 늘어남 |

공통 준비물: **Claude Code**가 설치되어 로그인된 상태, **Node.js LTS** (`node -v`로 확인).

---

## 1. chrome-devtools-mcp — 클로드가 진짜 크롬을 열고 조작

**요구사항:** Node.js LTS, Google Chrome (공식 지원은 Chrome / Chrome for Testing만)

### 설치 (둘 중 하나)

**A. MCP만 설치 (가장 간단)** — 터미널에서:

```bash
claude mcp add chrome-devtools --scope user npx chrome-devtools-mcp@latest
```

**B. 플러그인으로 설치 (MCP + 스킬 7종 포함, 추천)** — Claude Code 안에서:

```
/plugin marketplace add ChromeDevTools/chrome-devtools-mcp
/plugin install chrome-devtools-mcp@chrome-devtools-plugins
```

설치 후 Claude Code를 재시작하고 `/mcp` 또는 `/skills`로 확인합니다.

### 첫 테스트

Claude Code에 이렇게 입력:

```
Check the performance of https://developers.chrome.com
```

크롬이 열리고 성능 측정이 진행되면 성공입니다.

### 주의
- 브라우저 안의 내용(로그인된 페이지 포함)을 AI가 읽을 수 있습니다. 민감한 정보가 있는 탭은 조심하세요.
- 성능 측정 시 URL이 Google CrUX API로 전송될 수 있습니다. 끄려면 `--no-performance-crux`.
- 사용 통계 수집을 끄려면 `--no-usage-statistics`.

---

## 2. agent-desktop (Vercel) — AI가 내 컴퓨터 앱을 직접 조작

스크린샷이 아니라 운영체제의 **접근성 트리**(버튼 목록)를 읽고 누릅니다. 기본은 headless라 내 마우스를 움직이지 않습니다.

**요구사항**

| OS | 조건 |
|---|---|
| macOS | 13 이상 (Intel / Apple Silicon) |
| Windows | 10 1809 이상 (x64 / ARM64) |
| Linux | Ubuntu 24.04 이상, X11 또는 Wayland |

### 설치

```bash
npm install -g agent-desktop
```

(macOS/Linux는 `brew install agent-desktop`도 가능)

### 권한 확인 (macOS는 필수)

```bash
agent-desktop status
```

```bash
agent-desktop permissions --guide
```

macOS에서는 안내에 따라 **시스템 설정 → 개인정보 보호 및 보안 → 손쉬운 사용**에서 터미널(또는 Claude Code를 실행하는 앱)을 허용합니다.

### Claude Code에 연결 (둘 중 하나)

**A. 스킬 설치 (게시물에서 말한 "스킬 한 줄")**

```bash
npx skills add https://github.com/vercel-labs/agent-desktop --skill agent-desktop
```

**B. MCP 서버로 등록**

```bash
claude mcp add agent-desktop --scope user agent-desktop mcp
```

### 첫 테스트 (macOS 예시)

```bash
agent-desktop snapshot --app Finder -i
```

또는 Claude Code에 "Finder에서 다운로드 폴더 열어줘"라고 요청합니다.

### 주의
- 화면의 앱 내용이 AI로 전송됩니다. **보여주기 싫은 창(메신저, 은행, 비밀번호 관리자)은 닫고** 사용하세요.
- `--headed` 플래그를 주면 실제 마우스/키보드를 움직이므로 작업 중에는 손대지 마세요.

---

## 3. Craft Anything — 웹사이트·앱·게임을 분석(Study)하고 새로 제작(Build)

Study → Build → Check → Ready 4단계로 진행되며, 빌드는 내 컴퓨터의 Claude Code(`claude`)가 담당합니다.

### 설치 (쉬운 방법: 설치 파일)

[Releases 페이지](https://github.com/storytold/craft-anything/releases/latest)에서 OS에 맞는 파일을 받습니다.

- **Windows:** 설치 파일(코드 서명됨) 또는 포터블 zip
- **macOS:** `craft-anything-<버전>-macos-universal.dmg` (서명·공증됨, CLI 포함)
- **Linux:** AppImage 추천 (Flatpak은 샌드박스 때문에 웹사이트 분석용 브라우저를 못 띄움)

> 아직 **알파** 단계입니다. Claude Code를 빌더로 쓰는 웹사이트·앱·게임 워크플로는 동작하지만, 다른 에이전트 연동은 검증 중입니다.

### 설치 확인

```bash
craft-anything-cli doctor
```

설치 상태, 코딩 에이전트(Claude Code) 로그인 여부, 엔진을 점검합니다.

### 사용

앱에서: **무엇을 만들까요?** → 웹사이트/앱/게임 선택 → 주소 입력 → **Clean room**(기본) 또는 **Deep** 선택 → Start

CLI에서:

```bash
craft-anything-cli rebuild - "A snake game in Rust" --rounds 1
```

### 소스에서 빌드하려면 (선택)

Rust 1.97 이상 필요 (`rustup update`). 저장소를 받은 뒤:

```bash
cargo install --path apps/craft-anything-cli
```

### 주의 (중요)
- **내가 소유했거나 분석 허락을 받은 소프트웨어에만** 사용하세요 (README의 Responsible use).
- 기본값인 **Clean room** 모드는 원본의 코드·파일을 읽지 않고 겉으로 보이는 것만 참고합니다. **Deep** 모드는 내부까지 분석하므로 권리 관계를 더 신경 써야 합니다.
- 남의 서비스 로고·사진·문구·브랜드를 그대로 베끼면 저작권·상표 문제가 됩니다. "구조를 참고해 내 디자인으로 만들기"로 쓰세요.
- 빌드 중 의존성 설치(`npm install` 등)가 내 컴퓨터에서 코드를 실행합니다. 신뢰하는 프로젝트에서만 승인하세요.

---

## 클라우드 세션에서 확인한 것 (2026-11-26)

- `chrome-devtools-mcp` CLI를 클라우드 컨테이너에 설치하고, 로컬 테스트 페이지에서 **열기 → 요소 스냅샷 → 버튼 클릭 → 콘솔 로그 읽기 → 스크린샷**이 모두 정상 동작함을 확인했습니다.
- 외부 웹사이트 접속은 해당 클라우드 환경의 네트워크 정책으로 막혀 있어 시험하지 못했습니다.
- `agent-desktop`은 데스크톱 화면이 있는 내 컴퓨터가 필요하고, `Craft Anything`은 내 컴퓨터의 Claude Code를 빌더로 쓰기 때문에 클라우드에서는 실행하지 않았습니다.
