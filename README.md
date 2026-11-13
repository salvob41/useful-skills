# Useful Skills

Skills for Claude Code and Codex that fill gaps in [reidemeister94/development-skills](https://github.com/reidemeister94/development-skills). That plugin decides how the agent works: plan, implement, verify, review. This repository adds what it leaves out, such as root-cause debugging, language conventions, ADRs, PostgreSQL access and release setup.

## Recommended setup

These skills work best together:

| Skill | Source | What it does |
|---|---|---|
| `development-skills` | [reidemeister94/development-skills](https://github.com/reidemeister94/development-skills) | Gives the agent a development loop: it challenges the request, plans work that needs decisions, checks the result and runs an independent review |
| `useful-skills` | This repository | Adds the skills listed under [Skills](#skills) |
| `ponytail` | [DietrichGebert/ponytail](https://github.com/DietrichGebert/ponytail) | Makes the agent write the smallest change that solves the task |
| `unslop` | [cursor/plugins](https://github.com/cursor/plugins) | Removes AI writing patterns from prose, such as filler, dashes and inflated words |
| `find-skills` | [vercel-labs/skills](https://github.com/vercel-labs/skills) | Searches [skills.sh](https://skills.sh/) for a skill when you ask "is there a skill for X?" |

### Claude Code

```bash
/plugin marketplace add reidemeister94/development-skills
/plugin install development-skills@development-skills
/plugin marketplace add salvob41/useful-skills
/plugin install useful-skills@useful-skills
```

### Codex

```bash
codex plugin marketplace add reidemeister94/development-skills
codex plugin marketplace add salvob41/useful-skills
```

### Single skills, for any agent

This needs Node.js 22.20 or newer. `-g` installs for your user instead of the current project:

```bash
npx skills add DietrichGebert/ponytail --skill ponytail -g
npx skills add cursor/plugins --skill unslop -g
npx skills add vercel-labs/skills --skill find-skills -g
```

The same command installs this repository's skills on agents without plugin support, with `npx skills add salvob41/useful-skills`. These skills use Claude Code frontmatter fields such as `user-invocable`, which other agents usually ignore. Only Claude Code has been tested.

### Global setup

My global `CLAUDE.md`, and an installer for `ponytail`, `unslop` and `find-skills`, are in [this gist](https://gist.github.com/salvob41/5c095fd1282edefb6d6894ee9d2ddb03). The installer links the gist's `CLAUDE.md` to `~/.claude/CLAUDE.md` and backs up any existing file. Rerun it to update the skills:

```bash
gh gist clone 5c095fd1282edefb6d6894ee9d2ddb03 ~/claude-global
bash ~/claude-global/install.sh
```

## Skills

| Skill | What it does |
|---|---|
| `debugging` | Reproduces the bug, traces it to the root cause and writes a failing test before any fix. It stops after three failed fixes and questions the design |
| `python-dev`, `java-dev`, `typescript-dev`, `frontend-dev`, `swift-dev` | Load the conventions for each language and framework before the agent changes code |
| `adr`, `adr-list`, `adr-update` | Create, list and update Architecture Decision Records |
| `project-documenter` | Documents a codebase from its code, DDLs and scripts, including ER diagrams and ETL flows. It asks instead of guessing about legacy systems |
| `toolbox-setup` | Connects the agent to PostgreSQL through Google's [MCP Toolbox](https://github.com/googleapis/mcp-toolbox). By default it gives you the SQL for a read-only database role |
| `setup-make-bump` | Adds Commitizen, `make bump`, `make push-tag` and a commit-msg hook, then checks them with a dry run |
| `update-precommit` | Updates `.pre-commit-config.yaml` hooks to their latest versions |
| `update-reqs` | Updates `requirements.in` files to the latest PyPI versions and keeps each pin style |
| `get-api-docs` | Fetches current docs for a third-party API before the agent writes code against it |
| `create-skills` | Writes or fixes `SKILL.md` files, including skills that don't trigger |
| `best-practices-research` | Researches a topic in four phases with parallel searches, filters sources by authority and writes a cited report |
| `roast-my-code-audit` | Runs upstream's staff reviewer, then scores how well an AI agent can work in the repository across 12 dimensions |

### Differences from upstream's `best-practices` and `roast-my-code`

Upstream's `best-practices` is a 23-line instruction to search good sources and cite them. `best-practices-research` adds a fixed process: it splits the topic into research angles, runs the searches in parallel subagents, filters every source through quality gates and writes the report from a template. For Claude Code topics, it reads the official docs and recent release notes first.

Upstream's `roast-my-code` runs `staff-review` and retells the findings with humor. Its `--fix` option fixes the critical and high findings you choose. `roast-my-code-audit` has no `--fix`. It adds an AI-readiness audit that scores context files, tests, types, error messages and 8 other dimensions from 0 to 3, then gives a grade.

## PostgreSQL with MCP Toolbox

1. Install Toolbox with `brew install mcp-toolbox`, or download it from the [releases page](https://github.com/googleapis/mcp-toolbox/releases).
2. Run `/toolbox-setup`. It asks for a connection name, the connection details, and whether the agent may write.
3. Restart your MCP host if the new server doesn't appear.

The generated config holds the password in plain text, so add it to `.gitignore`. Toolbox's prebuilt configs are meant for trusted developers working with an agent. Don't use them to give an application's end users database access.

## License

MIT. Some skills started in [reidemeister94/development-skills](https://github.com/reidemeister94/development-skills).
