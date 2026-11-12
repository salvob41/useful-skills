# Useful Skills

One plugin for Claude Code and Codex. It complements the `development-skills` plugin from `reidemeister94/development-skills`, which is installed alongside it.

## Structure

```
skills/<name>/SKILL.md            # Every skill. Claude Code, Codex and `npx skills` all read this folder
.claude-plugin/plugin.json        # Claude Code plugin manifest and version
.claude-plugin/marketplace.json   # Claude Code marketplace
.codex-plugin/plugin.json         # Codex plugin manifest
.agents/plugins/marketplace.json  # Codex marketplace
```

## Conventions

- Skills follow the SKILL.md frontmatter specification and may use Claude Code fields.
- A skill must not link to files outside its own folder. Refer to upstream behavior by name, for example "the development loop from the `development-skills` plugin".
- Keep skill names distinct from upstream skill names, so both plugins can be installed together.
- Change the version in `.claude-plugin/plugin.json`, `.claude-plugin/marketplace.json` and `.codex-plugin/plugin.json` together.

## Model behavior

1. Be honest. If the developer's approach is wrong, say so with evidence.
2. Check every request for flaws, contradictions, wrong assumptions, and symptoms mistaken for causes.
3. Raise only concrete, evidence-based, actionable objections. If the request is sound, proceed.
4. On complex tasks, invest in planning: ask hard questions and remove ambiguity.
5. Validate approaches against sources and codebase evidence. Ask the developer when ambiguity remains.
6. Write discoveries worth keeping to disk: plans, chronicles and memory files.
