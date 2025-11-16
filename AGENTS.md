# Agent Guidelines for Kikiriki

## Project Overview
Bash-based push-to-talk speech-to-text tool for Ubuntu Linux using whisper.cpp and xbindkeys.

## Testing
- Manual test: `./kikiriki start` (speak something) then `./kikiriki stop`
- Service test: `systemctl --user status kikiriki.service`
- Check logs: `journalctl --user -u kikiriki.service -f`

## Code Style
- **Shebang**: `#!/bin/bash` for all shell scripts
- **Error handling**: Use `set -euo pipefail` at script start
- **Variables**: UPPER_CASE for configuration, lowercase for local vars
- **Quoting**: Always quote variables: `"$VAR"`, `"${VAR}"`
- **Conditionals**: Use `[[ ]]` for tests, not `[ ]`
- **Commands**: Prefer `command -v` over `which` for checking binaries
- **Cleanup**: Always cleanup temp files with `rm -f`
- **Functions**: Define functions before main logic; use snake_case naming
- **Exit codes**: 0 for success, 1 for errors

## Configuration
- Some options configurable via variables at kikiriki.conf (WHISPER_MODEL, WHISPER_BIN, etc.)
- Less important options can be set at the top of the `kikiriki` script
- State files in `~/.local/state/kikiriki/`
- Config files in `~/.config/kikiriki/`
