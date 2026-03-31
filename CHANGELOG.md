# Changelog

## [1.1.0] - 2026-03-31

### Added
- **Window management**: list-windows, new-window, focus-window, close-window, move-workspace-to-window (entire layer was missing)
- **Tab/Surface management**: rename-tab, tab-action, move-surface, reorder-surface, select-workspace, workspace-action
- **Claude integration**: claude-hook (session lifecycle), claude-teams, markdown viewer (live reload)
- **Panel commands**: list-panels, focus-panel, send-panel, send-key-panel
- **Buffer commands**: set-buffer, list-buffers, paste-buffer
- **Sidebar read-back**: sidebar-state, list-status, list-log, list-notifications, clear-notifications
- **Browser commands** (~30 new): back/forward/reload, find (Playwright locators), dialog, press/keydown/keyup, select, scroll, viewport, network, cookies, storage, tab management, highlight, state save/load, script/style injection
- **Browser snapshot options**: --compact, --max-depth, --selector, --cursor (token optimization)
- **tmux compat**: resize-pane, swap-pane, break-pane, join-pane, next/previous/last-window, last-pane, clear-history, respawn-pane, display-message, buffer commands
- **Socket Auth**: --password, CMUX_SOCKET_PASSWORD documentation
- **Environment**: CMUX_TAB_ID, --id-format refs|uuids|both
- **Codex CLI workaround**: send-key enter required after cmux send for Codex (cdd)

### Changed
- Expanded SKILL.md from 142 to ~240 lines (+70% coverage)
- Added --workspace flag documentation to all commands that support it
- Restructured browser section into semantic groups (navigation, snapshot, interaction, search, dialog, tabs)
- Updated rules/cmux-guide.md with new key commands and Codex workaround

### Verified By
- Claude Code (Opus) analyst subagent: line-by-line --help diff
- OpenAI Codex (gpt-5.4 xhigh): independent command gap analysis
- Gemini CLI: attempted (shell execution error, partial results)
- Triple cross-validation of all findings

## [1.0.0] - 2026-03-24

### Added
- Initial release
- Full cmux CLI reference (v0.62.2 verified via `cmux --help`)
- Browser automation commands (open, goto, snapshot, click, fill, wait, eval, screenshot, console, errors)
- Sidebar notification commands (notify, log, set-progress, set-status)
- tmux-to-cmux migration mapping table
- AI agent patterns (multi-agent parallel, output polling, dev server + browser E2E)
- Minimal rules file for always-on cmux directive (~66 tokens)
- 3-layer activation strategy (slash command, natural language, CLAUDE.md policy)

### Verified By
- `cmux --help` v0.62.2 full cross-check
- 19/19 live cmux execution tests
- 4-round multi-agent review
- OpenAI Codex cross-review (2 rounds)
