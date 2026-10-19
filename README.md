# agent-plugins

A Claude Code plugin marketplace named `cola`. It currently holds one plugin, `experts`,
as a stub for testing distribution. No real data or search logic yet.

```
.claude-plugin/marketplace.json      catalog: marketplace name + list of plugins
plugins/experts/
  .claude-plugin/plugin.json         plugin name ("experts" → /experts: prefix) + version
  skills/find/SKILL.md               /experts:find <topic>
  skills/profile/SKILL.md            /experts:profile <name>
  skills/refresh/SKILL.md            /experts:refresh (runs scripts/refresh.py)
  skills/gaps/SKILL.md               /experts:gaps
  scripts/refresh.py                 stub script
```

## Test locally (no GitHub needed)

In the Claude desktop app's Code tab (or `claude` in a terminal):

```
/plugin marketplace add /Users/boydga-admin/Documents/code/experts
/plugin install experts@cola
```

Or use **+ → Plugins → Add marketplace** and give it this folder's path.

Then try:

```
/experts:find groundwater rights in rural Texas
/experts:profile Albertson
/experts:refresh
/experts:gaps
```

Each stub replies with its name and version, so you can see which version is installed.

## Publish

1. Push this folder to a GitHub repo, e.g. `<account>/agent-plugins`.
2. A client adds it with `/plugin marketplace add <account>/agent-plugins`, installs
   `experts@cola`, and turns on auto-update for the marketplace.

## Release a new version

1. Change the plugin.
2. Bump `"version"` in `plugins/experts/.claude-plugin/plugin.json` (and the version
   strings in the stubs, while they exist).
3. Push. Clients get the update the next time Claude Code checks (at startup, with
   auto-update on), or via the Plugins menu.

Pushes that don't bump the version don't reach clients.

## Add another client's plugin

Add a folder under `plugins/`, then add an entry to `plugins` in
`.claude-plugin/marketplace.json`.
