# `install.sh` references missing path `scripts/handoff-prompt.sh` in v1.1.2-beta

## Summary

Running `install.sh` from the v1.1.2-beta release fails on the first `cp`                                                                                                                                                                      
because `scripts/handoff-prompt.sh` no longer exists in this version's tree.                                                                                                                                                                   
The file moved to per-platform subdirectories under `platforms/`, but                                                                                                                                                                          
`install.sh` was not updated to match.

## Environment

- Plugin: `claudikins-automatic-context-manager`
- Version: `1.1.2-beta`
- Installed via: `claudikins-marketplace` (Claude Code plugin system)
- Host: macOS (Darwin 25.4.0), zsh

## Repro

```bash         
bash ~/.claude/plugins/cache/claudikins-marketplace/claudikins-automatic-context-manager/1.1.2-beta/install.sh
```
                                                                                                                                                                                                                                              
### Actual output

```                                                                                                                                                                                                                                           
→ Installing handoff-prompt.sh
cp: .../1.1.2-beta/scripts/handoff-prompt.sh: No such file or directory                                                                                                                                                                        
✗ Failed to copy script
Exit code: 1. The script aborts after creating ~/.claude/claudikins-acm/                                                                                                                                                                       
but before installing the hook, skills, or statusline.                                                                                                                                                                                         
```

## Root cause      

```bash
#install.sh line ~57:

if ! cp "$SCRIPT_DIR/scripts/handoff-prompt.sh" "$SCRIPTS_DIR/"; then
```
                                                                                                                                                                                                                                               
But in `1.1.2-beta`, `scripts/` contains only:                                                                                                                                                                                                     
                                                                                                                                                                                                                                               
```
scripts/                                                                                                                                                                                                                                       
├── capture-state.sh
├── run-handoff.sh
└── statusline-command.sh
```

`handoff-prompt.sh` lives under per-platform dirs instead:                                                                                                                                                                                       
 
```
platforms/                                                                                                                                                                                                                                     
├── generic/handoff-prompt.sh
├── linux-zenity/handoff-prompt.sh
└── macos/handoff-prompt.sh                                                                                                                                                                                                                    
```

The packaging changed (platform-specific dialogs — sensible) but `install.sh` still copies from the old location.
                                                                                                                                                                                                                                               
## Suggested fix                                                                                                                                                                                                                                  
 
Detect the platform and pick the right variant:                                                                                                                                                                                                

```bash                
case "$OSTYPE" in
  darwin*)  PLATFORM=macos ;;
  linux*)   if command -v zenity >/dev/null 2>&1; then
              PLATFORM=linux-zenity                                                                                                                                                                                                            
            else
              PLATFORM=generic                                                                                                                                                                                                                 
            fi ;;
  *)        PLATFORM=generic ;;                                                                                                                                                                                                                
esac
                                                                                                                                                                                                                                               
cp "$SCRIPT_DIR/platforms/$PLATFORM/handoff-prompt.sh" "$SCRIPTS_DIR/handoff-prompt.sh"                                                                                                                                                        
```

Also worth verifying `uninstall.sh` and any update path don't assume the old layout.     
                                                                                                                                                                                                                                                
## Workaround used                                                                                                                                                                                                                                
                                                                                                                                                                                                                                               
Manual install: removed the dangling symlinks, then copied                                                                                                                                                                                     
`platforms/macos/handoff-prompt.sh`, `hooks/scripts/session-start.sh`,
the two `SKILL.md` files, and `scripts/statusline-command.sh` to their                                                                                                                                                                             
intended targets under `~/.claude/.` SessionStart hook was already
registered in `~/.claude/settings.json` from the prior install attempt.
