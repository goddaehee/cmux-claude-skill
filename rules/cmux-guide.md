# cmux 터미널 환경

이 환경은 cmux (AI 에이전트용 macOS 터미널)를 사용합니다. tmux 대신 cmux CLI를 사용하세요.
환경변수 `CMUX_WORKSPACE_ID`, `CMUX_SURFACE_ID`가 자동 설정되어 --workspace/--surface 생략 가능.
소켓: `~/Library/Application Support/cmux/cmux.sock`
핵심 명령: `read-screen`, `capture-pane`, `send`, `browser`, `wait-for`, `identify`, `tree`
상세 가이드: `/cmux` 스킬 참조. SSH/CI에서는 cmux 불가 → tmux 폴백.
