---
name: cmux
description: "cmux CLI reference for AI agents: workspace/pane/surface management, send/read-screen, browser automation, sidebar notifications, tmux migration. 터미널 분할, 출력 캡처, 브라우저 자동화, 멀티에이전트 병렬 실행. cmux --help v0.62.2 검증."
---

# cmux — AI 에이전트 터미널 가이드

**Version**: v0.62.2 (2026-03-31 검증, `cmux --help` 기반)

## 사전 조건
- macOS 14+, `brew tap manaflow-ai/cmux && brew install --cask cmux`
- cmux 앱 실행 중이어야 CLI 동작
- 환경변수 자동 설정: `CMUX_WORKSPACE_ID`, `CMUX_SURFACE_ID`, `CMUX_SOCKET_PATH`, `CMUX_TAB_ID`

## 계층
```
Window > Workspace(사이드바 탭) > Pane(분할) > Surface(터미널/브라우저 탭)
```
참조: `window:1`, `workspace:1`, `pane:1`, `surface:1`, `tab:1` 또는 UUID.
환경변수로 --workspace/--surface 생략 가능. 출력 형식: `--id-format refs|uuids|both` (기본: refs).

## Socket Auth
`--password` > `CMUX_SOCKET_PASSWORD` 환경변수 > Settings 저장 비밀번호 순 우선.

## 핵심 CLI

### 탐색
| 명령어 | 설명 |
|--------|------|
| `cmux identify [--no-caller]` | 현재 컨텍스트 (JSON) |
| `cmux tree [--all] [--workspace <ref>]` | 전체 계층 트리 |
| `cmux list-workspaces` | 워크스페이스 목록 |
| `cmux list-panes [--workspace <ref>]` | 패인 목록 |
| `cmux list-pane-surfaces [--pane <ref>]` | 패인 내 서피스(탭) 목록 |
| `cmux list-panels [--workspace <ref>]` | 패널 목록 |
| `cmux find-window [--content] [--select] "query"` | 내용으로 서피스 검색 |
| `cmux capabilities` | 소켓 메서드 + 프로토콜 버전 (JSON) |
| `cmux sidebar-state [--workspace <ref>]` | 사이드바 전체 상태 한번에 조회 (cwd, git, ports, status, progress, logs) |

### Window 관리
| 명령어 | 설명 |
|--------|------|
| `cmux list-windows` | 윈도우 목록 |
| `cmux current-window` | 현재 윈도우 |
| `cmux new-window` | 새 윈도우 |
| `cmux focus-window --window <ref>` | 윈도우 포커스 |
| `cmux close-window --window <ref>` | 윈도우 닫기 |
| `cmux move-workspace-to-window --workspace <ref> --window <ref>` | 워크스페이스를 다른 윈도우로 이동 |

### 생성/관리
| 명령어 | 설명 |
|--------|------|
| `cmux <path>` | 디렉토리를 새 워크스페이스에서 열기 (앱 미실행 시 자동 시작) |
| `cmux new-workspace [--cwd <path>] [--command "cmd"]` | 워크스페이스 생성 |
| `cmux select-workspace --workspace <ref>` | 워크스페이스 선택(전환) |
| `cmux rename-workspace [--workspace <ref>] "name"` | 워크스페이스 이름 변경 |
| `cmux close-workspace --workspace <ref>` | 워크스페이스 닫기 |
| `cmux workspace-action --action <name> [--workspace <ref>]` | 액션: pin, unpin, close-others, close-above, close-below, move-up/down/top, mark-read/unread |
| `cmux new-split <left\|right\|up\|down> [--workspace <ref>] [--surface <ref>]` | 패인 분할 |
| `cmux new-pane [--type <terminal\|browser>] [--direction <dir>] [--url <url>]` | 패인 생성 (타입/방향/URL 지정) |
| `cmux focus-pane --pane <ref> [--workspace <ref>]` | 패인 포커스 |
| `cmux new-surface [--type <terminal\|browser>] [--pane <ref>] [--url <url>]` | 서피스(탭) 추가 |
| `cmux close-surface [--surface <ref>] [--workspace <ref>]` | 서피스 닫기 |
| `cmux rename-tab [--surface <ref>] <title>` | **서피스 탭 이름 변경** |
| `cmux tab-action --action <name> [--tab <ref>]` | 탭 액션: rename, close-left/right/others, pin/unpin, reload, duplicate, new-terminal-right/new-browser-right |
| `cmux move-surface --surface <ref> [--pane <ref>] [--window <ref>]` | 서피스를 다른 패인/윈도우로 이동 |
| `cmux reorder-surface --surface <ref> (--index <n> \| --before <ref> \| --after <ref>)` | 서피스 순서 변경 |

### 전송 & 캡처 (핵심)
| 명령어 | 설명 |
|--------|------|
| `cmux send [--surface <ref>] "text\n"` | 서피스에 전송 (\n=Enter) |
| `cmux send-key [--surface <ref>] <key>` | 키 전송 (enter, ctrl+c, tab 등) |
| `cmux send-panel --panel <ref> <text>` | 패널에 직접 전송 |
| `cmux send-key-panel --panel <ref> <key>` | 패널에 키 전송 |
| `cmux read-screen [--surface <ref>] [--lines <n>] [--scrollback]` | **출력 읽기** |
| `cmux capture-pane [--surface <ref>] [--lines <n>] [--scrollback]` | tmux 호환 별칭 |
| `cmux wait-for [-S\|--signal] <name> [--timeout <sec>]` | 시그널 대기(-S 없이) / 보내기(-S) |
| `cmux set-buffer [--name <name>] <text>` | 버퍼에 텍스트 저장 |
| `cmux list-buffers` | 버퍼 목록 |
| `cmux paste-buffer [--name <name>] [--surface <ref>]` | 버퍼 내용 붙여넣기 |

### Claude 통합
| 명령어 | 설명 |
|--------|------|
| `cmux claude-hook <event> [--surface <ref>]` | 이벤트: session-start, active, stop, idle, notification, prompt-submit |
| `cmux claude-teams [claude-args...]` | Claude Teams 모드 시작 |
| `cmux markdown [open] <path>` | 마크다운 뷰어 패널 (라이브 리로드, 문서 리뷰에 유용) |

## 내장 브라우저 (WebKit)

`cmux browser [--surface <ref>] <subcommand>` — `--snapshot-after`로 자동 스냅샷.

### 탐색 & 상태
```
browser open [url]                          # 브라우저 분할 생성 (서피스 지정 시 navigate처럼 동작)
browser open-split [url]                    # 명시적 분할 생성
browser goto|navigate <url> [--snapshot-after]
browser back|forward|reload [--snapshot-after]
browser url|get-url                         # 현재 URL 반환
browser wait [--selector <css>] [--text <t>] [--url-contains <t>] [--load-state <interactive|complete>] [--function <js>] [--timeout-ms <ms>]
```

### DOM 스냅샷 & 스크린샷
```
browser snapshot [-i|--interactive] [--cursor] [--compact] [--max-depth <n>] [--selector <css>]
browser screenshot [--out <path>] [--json]
browser highlight <selector>                # 요소 시각적 하이라이트
```
> `--compact`와 `--max-depth`로 토큰 절약 가능. `--selector`로 특정 영역만 스냅샷.

### 인터랙션
```
browser click|dblclick|hover|focus <selector> [--snapshot-after]
browser check|uncheck <selector> [--snapshot-after]
browser scroll-into-view <selector> [--snapshot-after]
browser type <selector> <text> [--snapshot-after]       # 기존 값에 추가
browser fill <selector> [text] [--snapshot-after]       # 값 대체 (빈 text = 클리어)
browser press|keydown|keyup <key> [--snapshot-after]    # 키보드 (Enter, Escape 등)
browser select <selector> <value> [--snapshot-after]    # 드롭다운
browser scroll [--selector <css>] [--dx <n>] [--dy <n>] [--snapshot-after]
```

### 검색 & 프레임
```
browser find <role|text|label|placeholder|alt|title|testid|first|last|nth> ...
browser frame <selector|main>               # iframe 전환
browser get <url|title|text|html|value|attr|count|box|styles> [selector]
browser is <visible|enabled|checked> <selector>
browser eval <script>
```
> `browser find`는 Playwright 스타일 로케이터 — CSS 셀렉터보다 견고.

### 다이얼로그 & 네트워크
```
browser dialog <accept|dismiss> [text]      # JS alert/confirm/prompt 처리
browser download [wait] [--path <path>] [--timeout-ms <ms>]
browser cookies <get|set|clear> [...]
browser storage <local|session> <get|set|clear> [...]
browser network <route|unroute|requests>    # 네트워크 가로채기/모킹
browser viewport <width> <height>           # 뷰포트 크기 설정
```

### 탭 & 스크립트 주입
```
browser tab <new|list|switch|close|<index>>
browser addinitscript|addscript <script>    # JS 주입
browser addstyle <css>                      # CSS 주입
browser state <save|load> <path>            # 브라우저 상태 저장/로드
browser console <list|clear>
browser errors <list|clear>
```

## 사이드바

```
cmux notify --title "제목" --body "내용" [--subtitle "부제"] [--workspace <ref>]
cmux list-notifications
cmux clear-notifications
cmux log --level <info|progress|success|warning|error> [--source "name"] [--workspace <ref>] -- "메시지"
cmux list-log [--limit <n>] [--workspace <ref>]
cmux clear-log [--workspace <ref>]
cmux set-status <key> <value> [--icon <name>] [--color <#hex>] [--workspace <ref>]
cmux list-status [--workspace <ref>]
cmux clear-status <key> [--workspace <ref>]
cmux set-progress <0.0-1.0> [--label "텍스트"] [--workspace <ref>]
cmux clear-progress [--workspace <ref>]
```

## tmux→cmux 변환

| tmux | cmux |
|------|------|
| `new-session -d -s N` | `new-workspace` + `rename-workspace N` |
| `split-window -v/-h` | `new-split down/right` |
| `send-keys -t P "cmd" C-m` | `send --surface P "cmd\n"` (패인에 탭 여러개면 `list-pane-surfaces`로 확인) |
| `capture-pane -t P -p` | `capture-pane --surface P` |
| `capture-pane -p -S -` | `read-screen --scrollback` |
| `kill-session -t N` | `close-workspace --workspace N` |
| `wait-for ch` | `wait-for ch [--timeout 30]` |
| `pipe-pane "cmd"` | `pipe-pane --command "cmd" [--surface <ref>]` |
| `resize-pane -L/-R/-U/-D N` | `resize-pane --pane <ref> -L\|-R\|-U\|-D [--amount N]` |
| `swap-pane -t T` | `swap-pane --pane <ref> --target-pane <ref>` |
| `break-pane` | `break-pane [--pane <ref>] [--surface <ref>] [--no-focus]` |
| `join-pane -t T` | `join-pane --target-pane <ref> [--no-focus]` |
| `select-window -n/-p/-l` | `next-window` / `previous-window` / `last-window` |
| `last-pane` | `last-pane [--workspace <ref>]` |
| `clear-history` | `clear-history [--surface <ref>]` |
| `set-buffer` / `paste-buffer` | `set-buffer [--name <n>] <text>` / `paste-buffer [--name <n>] [--surface <ref>]` |
| `respawn-pane` | `respawn-pane [--surface <ref>] [--command <cmd>]` |
| `display-message -p "text"` | `display-message [-p] <text>` |
| _(없음)_ | `browser`, `markdown`, `notify`, `claude-hook`, `sidebar-state`, `rename-tab` |

## AI 패턴

### 멀티에이전트
```bash
# ✅ 올바른 패턴: --dangerously-skip-permissions 필수 (파일 쓰기 등 도구 사용 허용)
cmux new-workspace --cwd /path/to/project \
  --command "claude -p 'task A' --dangerously-skip-permissions"
cmux rename-workspace "agent-A"

# ✅ 같은 워크스페이스 내 split으로 병렬 실행 (send 크로스-워크스페이스 제한 우회)
SPLIT=$(cmux new-split right)   # → "OK surface:N workspace:M"
SURF=$(echo "$SPLIT" | grep -o 'surface:[0-9]*')
cmux send --surface "$SURF" "claude -p 'task B' --dangerously-skip-permissions\n"

# ❌ 잘못된 패턴: --dangerously-skip-permissions 없으면 파일 저장 등 도구 사용 불가
cmux new-workspace --command "claude -p 'write a file'"  # 도구 거부됨

# ❌ 잘못된 패턴: 다른 워크스페이스 서피스로 send 불가
cmux send --surface surface:20 "cmd\n"  # "Surface is not a terminal" 에러
```

> **주의:** `claude -p` 종료 시 scrollback이 비워짐. `[DONE]` echo 시그널로 완료 감지 불가.
> 대신 결과 파일 존재 여부나 `cmux wait-for` 시그널을 활용할 것.

### 서피스 탭 이름 변경
```bash
cmux rename-tab --surface surface:2 "Codex"
cmux rename-tab --surface surface:13 "Gemini"
```

### 출력 캡처 & 대기
```bash
cmux read-screen --surface surface:2 --lines 30
# 폴링 대기
while ! cmux read-screen --lines 10 | grep -q "ready"; do sleep 1; done
# 시그널
cmux wait-for build-done --timeout 60  # 대기
cmux wait-for -S build-done            # 시그널
```

### 개발서버 + 브라우저
```bash
cmux new-split right
cmux send --surface surface:2 "npm run dev\n"
cmux browser open http://localhost:3000
cmux browser wait --text "Welcome" --timeout-ms 5000
cmux browser snapshot -i --compact
cmux browser click "button#submit" --snapshot-after
```

### 마크다운 뷰어
```bash
cmux markdown open ./docs/plan.md     # 라이브 리로드 뷰어 패널
```

## 주의사항
- `omc team` 내부는 tmux 직접 호출 — 수동 작업시만 cmux
- SSH/원격/CI: cmux 불가 → `command -v cmux >/dev/null || tmux ...`
- 사이드바 명령은 cmux 터미널 내부에서만 동작
- macOS 14+ 전용. Gatekeeper 경고 시: `xattr -cr /Applications/cmux.app`
- **`cmux send` 범위 제한**: 다른 워크스페이스의 서피스로는 전송 불가. 같은 워크스페이스 내 `new-split`으로 생성한 서피스에만 동작
- **`claude -p` 완료 감지**: 종료 시 scrollback이 초기화되므로 echo 시그널(`[DONE]`) 감지 불가. 대신 결과 파일 존재 확인(`ls`) 또는 `cmux wait-for` 시그널 방식 사용
- **`claude -p` 권한**: 파일 쓰기·도구 사용 시 반드시 `--dangerously-skip-permissions` 추가
- **Codex CLI (`cdd`)**: `cmux send "prompt\n"`의 `\n`이 Enter를 트리거하지 않는 경우 있음. `cmux send "prompt"` 후 `cmux send-key enter`를 별도 실행할 것. Claude Code(`ccd`)는 `\n` 정상 동작.

## 트러블슈팅
| 문제 | 해결 |
|------|------|
| `cmux: command not found` | 앱 실행 확인. `sudo ln -sf "/Applications/cmux.app/Contents/Resources/bin/cmux" /usr/local/bin/cmux` |
| 소켓 연결 실패 | cmux 앱 재시작. 소켓: `~/Library/Application Support/cmux/cmux.sock` |
| `CMUX_WORKSPACE_ID` 없음 | cmux 터미널 내에서만 자동 설정 |
| Ghostty 설정 충돌 | `~/.config/ghostty/config`에서 미지원 설정 주석 처리 |
| `Surface is not a terminal` (send 시) | 다른 워크스페이스 서피스 대상 불가. `new-split`으로 **같은 워크스페이스** 내 서피스 생성 후 send |
| `claude -p` 완료 후 scrollback 비어있음 | 프로세스 종료 시 scrollback 초기화됨. 완료 감지는 파일 존재 확인(`ls fizzbuzz.*`) 또는 `cmux wait-for` 시그널 사용 |
| `claude -p`가 파일을 저장하지 않음 | `--dangerously-skip-permissions` 플래그 누락. 도구 사용(Write 등)에 필수 |
| Socket Auth 오류 | `--password`, `CMUX_SOCKET_PASSWORD` 환경변수, 또는 Settings에서 비밀번호 확인 |
