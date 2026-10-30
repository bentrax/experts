# agent-plugins

A Claude Code plugin marketplace named `cola` (repo: `cola-laits/agent-plugins`).

## experts

Find College of Liberal Arts faculty experts for media requests and research highlights.
The plugin is skills plus a connector: the data and search live in colaweb's hosted
`cola-experts` MCP server.

```
.claude-plugin/marketplace.json      catalog: marketplace name + list of plugins
plugins/experts/
  .claude-plugin/plugin.json         plugin name ("experts" → /experts: prefix) + version
  .mcp.json                          the cola-experts connector (colaweb)
  skills/find/SKILL.md               /experts:find <topic or request>
  skills/profile/SKILL.md            /experts:profile <name or EID>
```

## Install

In the Claude desktop app, open **Settings** → **Customize** → **Plugins**:

1. **+ Add** → **Add marketplace**, enter `cola-laits/agent-plugins`, and click **Sync**.
2. On the **Discover** tab, install **Experts**.
3. Start a new session in the **Code** tab.

## Update

1. **Settings** → **Customize** → **Plugins** → **+ Add** → **Manage marketplaces**.
2. Next to **cola**, click **⋯** → **Check for updates**, then **Done**.
3. Back in **Plugins**, click **Experts** → **Update**.
4. Start a new session in the **Code** tab. Sessions already open keep the old version.

If it still acts like the old version, click **⋯** next to Experts → **Remove**, then install it
again from **Discover**.

## Use

In a Code session, type `/experts:find` with a topic or a pasted reporter request,
or `/experts:profile` with a name or EID. You can also just ask, e.g.
"Who in Liberal Arts could talk to a reporter about voter turnout?"

## Release a new version

1. Change the plugin.
2. Bump `"version"` in `plugins/experts/.claude-plugin/plugin.json`.
3. Commit and push. Users get it when they run the update steps above.

Pushes that don't bump the version don't reach clients.

## Add another plugin

Add a folder under `plugins/`, then add an entry to `plugins` in
`.claude-plugin/marketplace.json`. Plugins whose code must stay private go in a separate
private repo with its own marketplace.
