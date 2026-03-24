# Changelog

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
