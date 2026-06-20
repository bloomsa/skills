---
name: open
description: Open file paths mentioned in Claude's most recent response in VS Code. Use when the user types /open, or asks to "open that file", "open those in vscode", etc., referring to paths just mentioned in chat (not in tool output, which is already clickable).
---

# open

Opens file paths from Claude's own last prose response in VS Code, since Claude Code does not render OSC 8 hyperlinks for paths mentioned in prose (only for paths inside tool output like Read/Edit/Grep results).

## Steps

1. Look back at your own immediately preceding chat message (not tool output) in this conversation. Extract every file path mentioned, along with an optional `:line` or `:line:col` suffix (e.g. `server.mjs:243`).
2. Resolve each path:
   - If absolute, use as-is.
   - If relative, resolve against the current working directory.
   - Skip anything that isn't an actual file on disk (verify with a quick existence check) — don't guess or hallucinate paths.
3. If no file paths are found in the last response, tell the user there was nothing to open and stop.
4. If exactly one match, open it directly. If multiple, open all of them (VS Code supports multiple `--goto` args in one invocation).
5. Run `code --goto <path>:<line>` for each path with a line number, or `code <path>` for paths without one. Example for multiple files:
   ```
   code --goto /abs/path/server.mjs:243 --goto /abs/path/other.mjs:10
   ```
6. Report back concisely which file(s) were opened.
