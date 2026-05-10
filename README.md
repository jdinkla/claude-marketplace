# jdinkla-marketplace

A personal [Claude Code](https://code.claude.com) plugin marketplace.

A marketplace is a catalog of plugins (skills, slash commands, agents, hooks, MCP servers, LSP servers) that Claude Code users can install with one command. See the [official docs](https://code.claude.com/docs/en/plugin-marketplaces) for the full specification.

## Install this marketplace

```shell
/plugin marketplace add jdinkla/claude-marketplace
```

Then browse and install plugins:

```shell
/plugin
```

Or install a specific plugin directly:

```shell
/plugin install <plugin-name>@jdinkla-marketplace
```

To pull updates later:

```shell
/plugin marketplace update jdinkla-marketplace
```

## Repository layout

```
.
├── .claude-plugin/
│   └── marketplace.json    # The catalog. Lists every plugin and its source.
├── plugins/                # In-repo plugins live here (one directory each).
│   └── <plugin-name>/
│       ├── .claude-plugin/
│       │   └── plugin.json
│       └── skills/, commands/, agents/, hooks/, ...
├── CLAUDE.md               # Instructions for Claude when working in this repo.
└── README.md
```

`metadata.pluginRoot` is set to `./plugins` in `marketplace.json`, so plugin entries can use the bare directory name as their `source` (e.g. `"source": "my-plugin"` resolves to `./plugins/my-plugin`).

## Add a new plugin

### Option A — plugin lives in this repo

1. Create the plugin directory:
   ```shell
   mkdir -p plugins/<plugin-name>/.claude-plugin
   ```
2. Add `plugins/<plugin-name>/.claude-plugin/plugin.json`:
   ```json
   {
     "name": "<plugin-name>",
     "description": "<one line>",
     "version": "0.1.0"
   }
   ```
3. Add the plugin's content (`skills/`, `commands/`, `agents/`, `hooks/`, etc.) — see the [plugins reference](https://code.claude.com/docs/en/plugins-reference).
4. Register it in `.claude-plugin/marketplace.json`:
   ```json
   {
     "name": "<plugin-name>",
     "source": "<plugin-name>",
     "description": "<one line>"
   }
   ```

### Option B — plugin lives in another GitHub repo

```json
{
  "name": "<plugin-name>",
  "source": {
    "source": "github",
    "repo": "<owner>/<repo>"
  },
  "description": "<one line>"
}
```

Pin to a tag or commit with `"ref"` and/or `"sha"` fields. Other source types (`url`, `git-subdir`, `npm`) are also supported — see the [plugin sources docs](https://code.claude.com/docs/en/plugin-marketplaces#plugin-sources).

## Validate before pushing

```shell
claude plugin validate .
```

Or from inside Claude Code:

```shell
/plugin validate .
```

## Test locally before pushing

```shell
/plugin marketplace add /Volumes/JD/repositories/claude-marketplace
/plugin install <plugin-name>@jdinkla-marketplace
```

## Versioning

Two ways to version plugins:

- **Pin a version**: set `"version"` in `plugin.json` and bump it on every release. Users only get updates when the string changes.
- **Track HEAD**: omit `"version"` and every commit becomes a new version (the commit SHA is used).

Avoid setting `version` in both `plugin.json` and the marketplace entry — `plugin.json` always wins.

## License

MIT — see [LICENSE](LICENSE) if/when added.
