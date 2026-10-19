# agent-plugins

A Claude Code plugin marketplace named `cola` (repo: `cola-laits/agent-plugins`).

## experts

Find College of Liberal Arts faculty experts for media requests and research highlights.
The plugin is skills plus a connector: the data and search live in colaweb's hosted
`cola-experts` MCP server; users sign in with their UT EID the first time it's used.

```
.claude-plugin/marketplace.json      catalog: marketplace name + list of plugins
plugins/experts/
  .claude-plugin/plugin.json         plugin name ("experts" → /experts: prefix) + version
  .mcp.json                          the cola-experts connector (colaweb)
  skills/find/SKILL.md               /experts:find <topic or request>
  skills/profile/SKILL.md            /experts:profile <name or EID>
  skills/gaps/SKILL.md               /experts:gaps   (UT Experts coverage report)
```

## Install

In the Claude desktop app's Code tab:

```
/plugin marketplace add cola-laits/agent-plugins
/plugin install experts@cola
```

Turn on auto-update for the `cola` marketplace. The first `/experts:find` prompts for UT sign-in.

To test changes before pushing, add this folder as a marketplace instead:
`/plugin marketplace add /Users/boydga-admin/Documents/code/experts`.

## Release a new version

1. Change the plugin.
2. Bump `"version"` in `plugins/experts/.claude-plugin/plugin.json`.
3. Commit and push. Clients get it the next time Claude Code checks for updates.

Pushes that don't bump the version don't reach clients.

## Add another plugin

Add a folder under `plugins/`, then add an entry to `plugins` in
`.claude-plugin/marketplace.json`. Plugins whose code must stay private go in a separate
private repo with its own marketplace.
