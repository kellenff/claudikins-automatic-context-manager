---
name: acm:upgrade
description: Upgrade Claudikins ACM by re-running the installer — optionally pass platform
argument-hint: "[platform]"
allowed-tools:
  - Bash
---

# acm:upgrade Command

You are upgrading Claudikins ACM by re-running the installer. `install.sh` is idempotent and backs up existing files before overwriting them, so this is safe to run on an existing installation.

This is the correct command to run after updating the plugin version — for example, users on v1.1.0, v1.1.1-beta, or v1.1.2-beta should run `/acm:upgrade` to apply the latest `handoff-prompt.sh` and hook configuration.

If a platform argument was supplied, validate it against the allowed values (`macos`, `linux-zenity`, `generic`) and pass it as `CLAUDIKINS_PLATFORM` when invoking `install.sh`. If the argument is invalid, abort immediately with a clear error — do not run the installer.

If no argument was supplied, run `install.sh` without a platform override and let it auto-detect.

## Execute

```bash
if [ -n "$1" ]; then
    case "$1" in
        macos|linux-zenity|generic)
            echo "Running: CLAUDIKINS_PLATFORM=$1 bash ${CLAUDE_PLUGIN_ROOT}/install.sh"
            CLAUDIKINS_PLATFORM="$1" bash "${CLAUDE_PLUGIN_ROOT}/install.sh"
            ;;
        *)
            echo "Invalid platform '$1'. Valid: macos, linux-zenity, generic." >&2
            exit 1
            ;;
    esac
else
    echo "Running: bash ${CLAUDE_PLUGIN_ROOT}/install.sh (auto-detect platform)"
    bash "${CLAUDE_PLUGIN_ROOT}/install.sh"
fi
```
