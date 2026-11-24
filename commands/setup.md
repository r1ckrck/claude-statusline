---
description: Point Claude Code's status line at this plugin (run once per device)
allowed-tools: Bash, Read, Edit
---

Set up the statusline plugin on this device.

1. Copy the script to the plugin's data folder, which keeps a stable path across plugin updates:

   ```
   mkdir -p "${CLAUDE_PLUGIN_DATA}" && cp "${CLAUDE_PLUGIN_ROOT}/statusline.sh" "${CLAUDE_PLUGIN_DATA}/statusline.sh" && chmod +x "${CLAUDE_PLUGIN_DATA}/statusline.sh"
   ```

2. Check the dependency: run `command -v jq`. If jq is missing, tell the user how to install it for their OS (`brew install jq`, `sudo apt install jq`, or `winget install jqlang.jq`) and stop.

3. Read the user's settings file: `$CLAUDE_CONFIG_DIR/settings.json` if that variable is set, else `~/.claude/settings.json`. If a `statusLine` entry already exists and differs, show it and ask before replacing it.

4. Set this entry, using the absolute path from step 1 with the home folder written as `~`, and keep every other key intact:

   ```json
   "statusLine": {
     "type": "command",
     "command": "bash ~/.claude/plugins/data/<data folder>/statusline.sh"
   }
   ```

5. Tell the user: the status line appears on the next refresh; icons need a Nerd Font (or set `"env": { "STATUSLINE_ICONS": "0" }` in settings for plain text); `extras/vscode-palette.json` in the plugin holds the pastel terminal palette it was designed with.
