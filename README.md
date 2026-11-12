# Useful Skills

Skills for coding agents that complement [reidemeister94/development-skills](https://github.com/reidemeister94/development-skills). That plugin provides the development loop, reviews and the general workflow skills. This repository adds language conventions, debugging, ADRs, project documentation, PostgreSQL access, release setup and dependency updates.

Install both. The language skills, `debugging` and `chronicles` follow the development loop from `development-skills`, and `roast-my-code-audit` uses its `staff-reviewer` agent.

## Installation

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

### Other agents

This needs Node.js 22.20 or newer:

```bash
npx skills add reidemeister94/development-skills
npx skills add salvob41/useful-skills
```

The skills use Claude Code frontmatter, such as `user-invocable` and `argument-hint`. Other agents usually ignore these fields. Only Claude Code has been tested.

## Skills

| Skill | What it does |
|---|---|
| `python-dev`, `java-dev`, `typescript-dev`, `frontend-dev`, `swift-dev` | Language and framework conventions, applied through the development loop |
| `debugging` | Finds the root cause before any fix |
| `chronicles` | Records why a task happened: the request, decisions and rejected options |
| `adr`, `adr-list`, `adr-update` | Create, browse and update Architecture Decision Records |
| `project-documenter` | Documents a codebase from code, DDLs and scripts, including ER diagrams and ETL flows |
| `toolbox-setup` | Connects the agent to PostgreSQL through Google's [MCP Toolbox](https://github.com/googleapis/mcp-toolbox) |
| `setup-make-bump` | Sets up Commitizen, `make bump`, `make push-tag` and a commit-msg hook |
| `update-precommit` | Updates `.pre-commit-config.yaml` hooks to their latest versions |
| `update-reqs` | Updates `requirements.in` files to the latest PyPI versions and keeps the pin style |
| `get-api-docs` | Fetches current third-party API docs before writing code against them |
| `create-skills` | Writes or improves `SKILL.md` files |
| `best-practices-research` | Researches current best practices and checks the authority of each source |
| `roast-my-code-audit` | Code review plus an AI-readiness audit scored across 12 dimensions |

`best-practices-research` and `roast-my-code-audit` are edited versions of upstream's `best-practices` and `roast-my-code`. They have different names so both versions can be installed.

## PostgreSQL with MCP Toolbox

1. Install Toolbox with `brew install mcp-toolbox`, or download it from the [releases page](https://github.com/googleapis/mcp-toolbox/releases).
2. Run `/toolbox-setup`. It asks for a connection name, the connection details, and whether the agent may write.
3. Restart your MCP host if the new server doesn't appear.

The generated config holds the password in plain text, so add it to `.gitignore`. Toolbox's prebuilt configs are meant for trusted developers working with an agent. Don't use them to give an application's end users database access.

## License

MIT. Some skills started in [reidemeister94/development-skills](https://github.com/reidemeister94/development-skills).
