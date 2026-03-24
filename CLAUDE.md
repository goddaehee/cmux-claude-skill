# cmux-claude-skill

This plugin teaches AI agents to use **cmux** (AI-native macOS terminal) instead of tmux.

## Key Points
- Use cmux CLI commands, not tmux, for terminal multiplexing on macOS.
- Environment variables `CMUX_WORKSPACE_ID`, `CMUX_SURFACE_ID` are auto-set in cmux terminals.
- For full CLI reference, invoke `/cmux` skill.
- Fall back to tmux in SSH/CI/remote environments where cmux is unavailable.
