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

```bash
# Check for cmux updates
brew outdated --cask cmux

# Update cmux
brew upgrade --cask cmux

# Verify new version
cmux version

# Update this plugin
claude plugin update cmux-claude-skill
```

Plugin maintainers: re-verify commands against `cmux --help` and update SKILL.md.

---

## Repo Structure

```
cmux-claude-skill/
├── skills/cmux/SKILL.md    # Full CLI reference (~240 lines, v0.62.2 verified)
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
