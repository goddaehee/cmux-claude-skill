# cmux-claude-skill

> Claude Code skill plugin for **cmux** — the AI-native macOS terminal.
> Teaches AI agents to use cmux instead of tmux for terminal multiplexing.

cmux is a new tool (released Feb 2026), so Claude's training data doesn't cover it.
This plugin provides verified CLI references and usage patterns, ensuring AI agents
can reliably operate cmux for workspace management, output capture, browser automation, and more.

**All commands verified against `cmux --help` v0.62.2, cross-checked by Claude Code + Codex + Gemini tri-model review.**

---

## Install

```bash
claude plugin marketplace add goddaehee/cmux-claude-skill
claude plugin install cmux-claude-skill
```

Restart Claude Code after installation.

---

## How to Use (3-Layer Activation)

cmux is new to AI models. Auto-triggering may be inconsistent.
Use these methods in order of reliability:

### 1. Slash Command (most reliable)

```
/cmux
```

This loads the full skill reference into the session. Best for first use.

### 2. Natural Language Triggers

Include **both `cmux` and `not tmux`** in your prompt for best results:

```
"cmux로 패인 분할해줘. tmux 말고 cmux 사용해."
"Use cmux to split panes and read output from the other terminal."
"Migrate this tmux workflow to cmux commands."
"cmux 브라우저로 localhost:3000 열어서 확인해줘."
```

### 3. CLAUDE.md Policy (best for persistent recognition)

Add to your project or global `CLAUDE.md`:

```markdown
## Terminal Policy
- Use cmux (not tmux) for local macOS terminal operations.
- Key commands: identify, tree, new-workspace, new-split, send, read-screen, browser, wait-for.
- Invoke `/cmux` for full CLI reference.
- Fall back to tmux only in SSH/CI/remote environments.
```

---

## What This Skill Provides

| Category | Commands |
|----------|----------|
| **Navigation** | `identify`, `tree`, `list-workspaces`, `list-panes`, `list-panels`, `find-window`, `capabilities`, `sidebar-state` |
| **Window** | `list-windows`, `new-window`, `focus-window`, `close-window`, `move-workspace-to-window` |
| **Management** | `new-workspace`, `new-split`, `new-pane`, `new-surface`, `close-workspace`, `close-surface`, `rename-tab`, `tab-action`, `workspace-action`, `move-surface` |
| **Send & Capture** | `send`, `send-key`, `send-panel`, `read-screen`, `capture-pane`, `set-buffer`, `paste-buffer` |
| **Synchronization** | `wait-for` (signal-based), polling patterns |
| **Claude Integration** | `claude-hook`, `claude-teams`, `markdown` (live reload viewer) |
| **Browser** | ~40 subcommands: navigation, snapshot, interaction, find (Playwright locators), dialog, network, cookies, storage, tabs, script injection |
| **Sidebar** | `notify`, `log`, `set-progress`, `set-status` + read-back: `list-status`, `list-log`, `list-notifications`, `sidebar-state` |
| **tmux Migration** | Full tmux-to-cmux mapping (20+ commands including resize, swap, break, join, buffers) |
| **AI Patterns** | Multi-agent parallel, rename-tab, output polling, dev server + browser E2E, markdown viewer |

---

## Verify Installation

```bash
# Check plugin is loaded
claude plugin list

# In Claude Code session:
/cmux
# Should display the full skill reference
```

### Functional Test

```
/cmux
Run cmux identify, then cmux tree to show the current layout.
```

If you see `workspace:1`, `pane:1`, `surface:1` — it's working.

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| `/cmux` not recognized | Restart Claude Code. Check `claude plugin list`. |
| AI still uses tmux commands | Start with `/cmux`. Add "not tmux" to prompt. Add CLAUDE.md policy. |
| `cmux: command not found` | Install: `brew tap manaflow-ai/cmux && brew install --cask cmux` |
| Socket connection failure | Restart cmux app. Check: `ls ~/Library/Application\ Support/cmux/cmux.sock` |
| `CMUX_WORKSPACE_ID` missing | Run commands inside cmux terminal (not iTerm2/regular terminal). |
| Doesn't work in SSH/CI | Expected. cmux is macOS-only. Use tmux for remote environments. |

---

## When cmux Updates

### 1. cmux 앱 업데이트

```bash
# 업데이트 확인
brew outdated --cask cmux

# 업데이트 실행
brew upgrade --cask cmux

# ⚠️ 중요: cmux 앱 재시작 필수 (CLI만 교체되고 실행 중인 앱은 구 버전)
# cmux 앱 종료 후 다시 실행, 또는:
open -a cmux

# 버전 확인
cmux version
```

### 2. 스킬 플러그인 업데이트

```bash
# 플러그인으로 설치한 경우
claude plugin update cmux-claude-skill

# 수동 설치(파일 복사)한 경우
git -C /path/to/cmux-claude-skill pull origin main
cp /path/to/cmux-claude-skill/skills/cmux/SKILL.md ~/.claude/skills/cmux/SKILL.md
cp /path/to/cmux-claude-skill/rules/cmux-guide.md ~/.claude/rules/cmux-guide.md
```

### 3. 원스텝 업데이트 스크립트

```bash
#!/bin/bash
# cmux-update.sh — cmux 앱 + 스킬 플러그인 한번에 업데이트
set -e

echo "=== cmux 앱 업데이트 ==="
brew upgrade --cask cmux 2>/dev/null && echo "✅ cmux 업그레이드 완료" || echo "ℹ️ 이미 최신 버전"
echo "cmux $(cmux version 2>&1 | head -1)"

echo ""
echo "=== 스킬 플러그인 업데이트 ==="
SKILL_DIR="${CMUX_SKILL_DIR:-$HOME/cmux-claude-skill}"
if [ -d "$SKILL_DIR/.git" ]; then
  git -C "$SKILL_DIR" pull origin main
  cp "$SKILL_DIR/skills/cmux/SKILL.md" ~/.claude/skills/cmux/SKILL.md
  cp "$SKILL_DIR/rules/cmux-guide.md" ~/.claude/rules/cmux-guide.md
  echo "✅ 스킬 동기화 완료"
else
  echo "⚠️ 스킬 디렉토리 없음: $SKILL_DIR"
  echo "   설치: git clone https://github.com/goddaehee/cmux-claude-skill.git $SKILL_DIR"
fi

echo ""
echo "=== ⚠️ cmux 앱을 재시작하세요 ==="
echo "   신규 기능(set-color 등)은 앱 재시작 후 동작합니다."
```

Plugin maintainers: re-verify commands against `cmux --help` and update SKILL.md.

---

## Workspace Color Customization

워크스페이스별 색상을 지정하면 사이드바에서 프로젝트를 빠르게 구분할 수 있습니다.

```bash
# 네임드 컬러 (16종)
# Red, Crimson, Orange, Amber, Olive, Green, Teal, Aqua,
# Blue, Navy, Indigo, Purple, Magenta, Rose, Brown, Charcoal

# 워크스페이스에 색상 적용
cmux workspace-action --workspace workspace:1 --action set-color --color Blue
cmux workspace-action --workspace workspace:2 --action set-color --color Amber
cmux workspace-action --workspace workspace:3 --action set-color --color Green

# HEX 코드도 지원
cmux workspace-action --action set-color --color "#C0392B"

# 색상 제거
cmux workspace-action --action clear-color
```

> **Tip:** AI 에이전트에게 "워크스페이스 색상 다르게 지정해줘"라고 요청하면 자동으로 구분 색상을 적용합니다.

---

## Repo Structure

```
cmux-claude-skill/
├── skills/cmux/SKILL.md    # Full CLI reference (~275 lines, v0.63.1 verified)
├── rules/cmux-guide.md     # Minimal always-on directive (7 lines, ~66 tokens)
├── .claude-plugin/
│   └── plugin.json          # Plugin metadata
├── CLAUDE.md                # Plugin-level instructions
├── README.md                # This file
├── CHANGELOG.md
├── LICENSE                  # MIT
└── package.json
```

---

## Requirements

- macOS 14+
- cmux installed and running (`brew tap manaflow-ai/cmux && brew install --cask cmux`)
- Claude Code

---

## 한국어 안내

### 설치
```bash
claude plugin marketplace add goddaehee/cmux-claude-skill
claude plugin install cmux-claude-skill
```

### 사용법
```
/cmux                                    # 슬래시 커맨드 (가장 확실)
"cmux로 패인 분할해줘. tmux 말고."         # 자연어 (cmux + not tmux 함께 명시)
```

### 자동 인식 강화
프로젝트 `CLAUDE.md`에 추가:
```markdown
## 터미널 정책
- tmux 대신 cmux를 사용하세요.
- 핵심 명령: identify, tree, new-split, send, read-screen, browser
- 상세 가이드: /cmux
```

### cmux가 뭔가요?
AI 에이전트를 위해 설계된 macOS 네이티브 터미널입니다.
내장 브라우저, 사이드바 알림, Unix socket API를 제공합니다.
공식: https://cmux.com / https://github.com/manaflow-ai/cmux

---

## Verification History

This skill was verified through:
- `cmux --help` v0.62.2 full command cross-check (v1.0.0, v1.1.0)
- 19/19 live cmux execution tests (split, send, read-screen, browser, sidebar)
- 4-round multi-agent review (factchecker, Korean reviewer, QA tester)
- OpenAI Codex cross-review (2 rounds, xhigh reasoning)
- Real-world surface:2 hardcoding bug discovery and fix
- v1.1.0: Claude Code (Opus) + Codex (gpt-5.4) + Gemini tri-model gap analysis
- v1.1.0: rename-tab live verification, ~70 missing commands identified and documented

---

## License

MIT

---

## Credits

- **cmux**: [manaflow-ai/cmux](https://github.com/manaflow-ai/cmux) (AGPL-3.0)
- **Skill author**: [goddaehee](https://github.com/goddaehee)
- **Blog**: [cmux 완전 가이드](https://goddaehee.tistory.com/557)
