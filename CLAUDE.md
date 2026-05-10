# CLAUDE.md

This repository is a **Claude Code plugin marketplace**. Its job is to publish a catalog (`marketplace.json`) of plugins that Claude Code users can install.

Authoritative docs: <https://code.claude.com/docs/en/plugin-marketplaces> and <https://code.claude.com/docs/en/plugins-reference>.

## What is here

- `.claude-plugin/marketplace.json` — the catalog. Every plugin offered by this marketplace is listed in its `plugins` array. `metadata.pluginRoot` is `./plugins`, so a `"source": "foo"` entry resolves to `./plugins/foo`.
- `plugins/<name>/` — in-repo plugins. Each one has its own `.claude-plugin/plugin.json` plus whatever components it ships (`skills/`, `commands/`, `agents/`, `hooks/`, `mcp.json`, etc.).

## When asked to add a plugin

1. Create `plugins/<name>/.claude-plugin/plugin.json` with `name`, `description`, and `version`.
2. Add the plugin's components under `plugins/<name>/` per the [plugins reference](https://code.claude.com/docs/en/plugins-reference).
3. Append a new entry to the `plugins` array in `.claude-plugin/marketplace.json`. Use the bare directory name as `source` (the `pluginRoot` makes `./plugins/` implicit).
4. Run `claude plugin validate .` before reporting done.

If the plugin lives in a separate repo, use a `source` object (`github`, `url`, `git-subdir`, or `npm`) instead — do not copy the plugin into this repo.

## Constraints

- **Plugin names** must be kebab-case (lowercase letters, digits, hyphens). Required by the Claude.ai marketplace sync.
- **Marketplace name** is `jdinkla-marketplace`. Don't rename it casually — users reference plugins as `<plugin>@jdinkla-marketplace`, so renaming breaks every install.
- **Reserved marketplace names** to never use: `claude-code-marketplace`, `claude-code-plugins`, `claude-plugins-official`, `anthropic-marketplace`, `anthropic-plugins`, `agent-skills`, `knowledge-work-plugins`, `life-sciences`, plus anything that impersonates Anthropic.
- **Relative `source` paths must start with `./`** and cannot use `..`.
- **Versioning**: don't set `version` in both `plugin.json` and the marketplace entry — the `plugin.json` value silently wins. Pick one place.
- **`${CLAUDE_PLUGIN_ROOT}`**, not relative paths, in hook commands and MCP server configs — plugins are copied to a cache directory at install time.

## Validation command

```shell
claude plugin validate .
```

Run this whenever `marketplace.json`, any `plugin.json`, or any skill/agent/command frontmatter changes.

## Don't

- Don't commit secrets, tokens, or `.env` files.
- Don't add a plugin entry without also creating the plugin directory (or pointing `source` at a real external repo).
- Don't reference files outside a plugin's own directory — the cache copy won't include them. Use symlinks if sharing is required.
