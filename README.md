# claude-statusline

A two-line status line for Claude Code. It makes the state you can't see — context, usage limits, cache, cost — permanently visible, without adding noise.

```
 󰚩 Opus 5.5  medium    ~/Documents/Projects/pencil-down   main · 32 hours ago

 cache 97% │ 󰚋 ctx █░░░░░░░░░ 10% │  7d 36% ↻ 1d 18h │  api 2m 46s │ $ 1.13
```

**Line one — where you are.** Model, effort level, folder, git branch.
**Line two — what you're spending.** Cache hit rate, context window, usage against the 5-hour and 7-day limits, time waiting on the API, session cost.

## Install

Requires `jq` and a [Nerd Font](https://www.nerdfonts.com/) set as your terminal font (or plain-text mode, below).

In Claude Code:

```
/plugin marketplace add r1ckrck/r1ckrck-claude-mods
/plugin install statusline@r1ckrck-claude-mods
/statusline:setup
```

`/statusline:setup` copies the script to the plugin's data folder and points `statusLine` in your `~/.claude/settings.json` at it. Run it once per device. Updates arrive through the marketplace and take effect at the next session start.

### Without the plugin

Copy `statusline.sh` anywhere, then add to `~/.claude/settings.json`:

```json
"statusLine": { "type": "command", "command": "bash ~/path/to/statusline.sh" }
```

## Reading it

### Line one

| Element | Meaning |
|---|---|
| Model | Pill colour by family — Opus violet, Sonnet blue, Haiku green. |
| Effort | Current `/effort` tier, cool to hot: `low` grey · `medium` cyan · `high` yellow · `xhigh` bright yellow · `max` red with ✦. |
| Folder | Working directory, `~` for home. |
| Branch | Ahead/behind the remote and time since the last commit. **Turns red when there are uncommitted changes.** |

### Line two

| Element | Meaning |
|---|---|
| cache % | Share of the last turn's input served from cache. Higher is cheaper and faster. |
| ctx % | How full the context window is. Matches `/context`. |
| 5h % ↻ | Usage against the rolling 5-hour limit, and time to reset. |
| 7d % ↻ | Usage against the 7-day limit, and time to reset. |
| api | Time this session spent waiting on the API. |
| $ | This session's cost. |

Segments without data are hidden — the limits only appear on subscription plans, after the first reply.

### Colour

Usage bars step from green to yellow, bright red, then bold red at 50 / 75 / 90%. Cache is reversed, because high is good: 70%+ green, 40–69% yellow, under 40% red. A sudden cache drop usually means something early in the conversation changed and invalidated everything after it.

**The two numbers worth acting on:** context above ~80% — start a fresh session before quality slips. 5h above ~85% — finish the current task before planning the next.

## Design

- **Hierarchy by weight.** Line one is loud — filled pills, because identity is what you check at a glance. Line two is quiet — dim labels, colour only on the values, so a number only draws the eye when it changes state.
- **Colour is a role, not a value.** Every colour is an ANSI 16 code, so your terminal theme resolves it. The status line never clashes with the theme; it inherits it. Same principle as design tokens.
- **Semantic polarity.** Colour means *good* or *act*, not *high* or *low* — which is why cache is reversed.

The pastel look in the screenshot comes from the palette in [`extras/vscode-palette.json`](extras/vscode-palette.json). Merge it into VS Code's `settings.json` to match.

## Options

| Env var | Effect |
|---|---|
| `STATUSLINE_ICONS=0` | Plain text: no Nerd Font icons or rounded caps. |

Set it in `~/.claude/settings.json` under `"env"`.

## Notes

- **Windows:** runs under Git Bash. Install jq with `winget install jqlang.jq` and confirm `jq --version` works in Git Bash specifically.
- **Data:** every number comes from the JSON Claude Code passes the script. Nothing is estimated or scraped.
- **Cost:** one `jq` call and a few `git` calls per refresh, roughly 10–30 ms.

## License

GPL-3.0-only
