---
name: claudikins-automatic-context-manager:install
description: Install Claudikins ACM (run install.sh) — optionally pass platform: macos | linux-zenity | generic
argument-hint: "[platform]"
allowed-tools:
  - Bash
---

# acm:install Command

You are running the Claudikins ACM installer.

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
