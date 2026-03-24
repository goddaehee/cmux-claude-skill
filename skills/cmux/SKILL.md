---
name: cmux
description: "cmux CLI reference for AI agents: workspace/pane/surface management, send/read-screen, browser automation, sidebar notifications, tmux migration. 터미널 분할, 출력 캡처, 브라우저 자동화, 멀티에이전트 병렬 실행. cmux --help v0.62.2 검증."
---

# cmux — AI 에이전트 터미널 가이드

**Version**: v0.62.2 (2026-03-23 검증, `cmux --help` 기반)

## 사전 조건
- macOS 14+, `brew tap manaflow-ai/cmux && brew install --cask cmux`
- cmux 앱 실행 중이어야 CLI 동작
- 환경변수 자동 설정: `CMUX_WORKSPACE_ID`, `CMUX_SURFACE_ID`, `CMUX_SOCKET_PATH`

## 계층
```
Window > Workspace(사이드바 탭) > Pane(분할) > Surface(터미널/브라우저 탭)
```
참조: `workspace:1`, `pane:1`, `surface:1` 또는 UUID. 환경변수로 --workspace/--surface 생략 가능.

## 핵심 CLI

### 탐색
| 명령어 | 설명 |
|--------|------|
| `cmux identify` | 현재 컨텍스트 (JSON) |
| `cmux tree [--all]` | 전체 계층 트리 |
| `cmux list-workspaces` | 워크스페이스 목록 |
| `cmux list-panes` | 패인 목록 |
| `cmux find-window [--content] [--select] "query"` | 내용으로 서피스 검색 |
| `cmux list-pane-surfaces [--pane <ref>]` | 패인 내 서피스(탭) 목록 |

### 생성/관리
| 명령어 | 설명 |
|--------|------|
| `cmux new-workspace [--cwd <path>] [--command "cmd"]` | 워크스페이스 생성 |
| `cmux rename-workspace "name"` | 이름 변경 |
| `cmux new-split <left\|right\|up\|down>` | 패인 분할 |
| `cmux new-surface [--type <terminal\|browser>] [--url <url>]` | 서피스 추가 |
| `cmux close-workspace --workspace <ref>` | 워크스페이스 닫기 |
| `cmux close-surface [--surface <ref>]` | 서피스 닫기 |

### 전송 & 캡처 (핵심)
| 명령어 | 설명 |
|--------|------|
| `cmux send "text\n"` | 현재 서피스에 전송 (\n=Enter) |
| `cmux send --surface <ref> "text\n"` | 특정 서피스에 전송 |
| `cmux send-key <key>` | 키 전송 (enter, ctrl+c, tab) |
| `cmux read-screen [--lines <n>] [--scrollback]` | **출력 읽기** |
| `cmux capture-pane [--lines <n>] [--scrollback]` | tmux 호환 별칭 |
| `cmux wait-for <name> [--timeout <sec>]` | 시그널 대기 |
| `cmux wait-for -S <name>` | 시그널 보내기 |

## 내장 브라우저 (WebKit)

`cmux browser [--surface <ref>] <subcommand>` — `--snapshot-after`로 자동 스냅샷.

```
cmux browser open [url]                     # 브라우저 분할 생성
cmux browser goto|navigate <url> [--snapshot-after]  # URL 이동
cmux browser snapshot [-i]                  # DOM 스냅샷 (요소 ref 반환)
cmux browser click "selector" [--snapshot-after]
cmux browser fill "selector" "value" [--snapshot-after]
cmux browser type "selector" "text" [--snapshot-after]
cmux browser wait [--selector <css>] [--text <t>] [--url-contains <t>] [--timeout-ms <ms>]
cmux browser get <url|title|text|html|value|attr|count|box|styles> [selector]
cmux browser is <visible|enabled|checked> "selector"
cmux browser eval "script"
cmux browser screenshot [--out <path>]
cmux browser console <list|clear>
cmux browser errors <list|clear>
```

## 사이드바

```
cmux notify --title "제목" --body "내용" [--subtitle "부제"]
cmux log --level <info|progress|success|warning|error> [--source "name"] "메시지"
cmux set-progress <0.0-1.0> [--label "텍스트"]
cmux set-status <key> <value> [--icon <name>] [--color <#hex>]
cmux clear-progress
cmux clear-status <key>
cmux clear-log
```

## tmux→cmux 변환

| tmux | cmux |
|------|------|
| `new-session -d -s N` | `new-workspace` + `rename-workspace N` |
| `split-window -v/-h` | `new-split down/right` |
| `send-keys -t P "cmd" C-m` | `send --surface P "cmd\n"` (tmux P=pane, cmux P=surface — 패인에 탭 여러개면 `list-pane-surfaces`로 확인) |
| `capture-pane -t P -p` | `capture-pane --surface P` |
| `capture-pane -p -S -` | `read-screen --scrollback` |
| `kill-session -t N` | `close-workspace --workspace N` |
| `wait-for ch` | `wait-for ch [--timeout 30]` |
| `pipe-pane "cmd"` | `pipe-pane --command "cmd"` |
| _(없음)_ | `browser open/snapshot -i/identify/notify` |

## AI 패턴

### 멀티에이전트
```bash
cmux new-workspace --command "claude -p 'task A'"
cmux rename-workspace "agent-A"
# 동적 ref: cmux new-split right → "OK surface:N workspace:M"
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
cmux browser snapshot -i
cmux browser click "button#submit" --snapshot-after
```

## 주의사항
- `omc team` 내부는 tmux 직접 호출 — 수동 작업시만 cmux
- SSH/원격/CI: cmux 불가 → `command -v cmux >/dev/null || tmux ...`
- 사이드바 명령은 cmux 터미널 내부에서만 동작
- macOS 14+ 전용. Gatekeeper 경고 시: `xattr -cr /Applications/cmux.app`

## 트러블슈팅
| 문제 | 해결 |
|------|------|
| `cmux: command not found` | 앱 실행 확인. `sudo ln -sf "/Applications/cmux.app/Contents/Resources/bin/cmux" /usr/local/bin/cmux` |
| 소켓 연결 실패 | cmux 앱 재시작. 소켓: `~/Library/Application Support/cmux/cmux.sock` |
| `CMUX_WORKSPACE_ID` 없음 | cmux 터미널 내에서만 자동 설정 |
| Ghostty 설정 충돌 | `~/.config/ghostty/config`에서 미지원 설정 주석 처리 |
