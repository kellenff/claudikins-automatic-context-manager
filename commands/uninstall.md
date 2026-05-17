---
name: acm:uninstall
description: Uninstall Claudikins ACM (run uninstall.sh) — will prompt for y/N confirmation
allowed-tools:
  - Bash
---

# acm:uninstall Command

You are running the Claudikins ACM uninstaller.

Run `uninstall.sh` directly. The script is interactive — it will prompt the user for y/N confirmation before removing any files. The user should respond in their next message.

## Execute

```bash
bash "${CLAUDE_PLUGIN_ROOT}/uninstall.sh"
```
