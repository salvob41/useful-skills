---
name: setup-make-bump
description: "Use when the user wants `make bump`, Commitizen, or conventional-commit versioning set up in a project, asks to bump or release a version in a project with no `make bump` target, or says /setup-make-bump."
user-invocable: true
allowed-tools: Bash, Read, Write, Edit, Glob
---

# Set Up make bump

This skill leaves the project with one release command: version, changelog, commit and annotated tag come from conventional commits, and nothing is pushed until `make push-tag`.

## Step 1: Inspect the project

Answer each question:

1. Is this a git repository? If not, run `git init -b main`.
2. Is the working tree clean?
3. Which files hold the version (`pyproject.toml`, `VERSION`, `__init__.py`, `package.json`)?
4. What is the latest `v*` tag?
5. Does a `bump` target in the `Makefile`, a `[tool.commitizen]` table or a `.cz.toml` already exist? Does another release tool (semantic-release, release-please, bump-my-version)?
6. Is this a Poetry project (`poetry.lock` or `[tool.poetry]`), and is `uv` installed?

If Q5 finds a `bump` target or Commitizen config, stop and change nothing: tell the user it's already set up, and use it if they asked for a release. If it finds another release tool, ask the user before adding a second one.

Pick the runner from Q6:

| Situation | `CZ` | `PRE_COMMIT` |
|---|---|---|
| Poetry | `poetry run cz` | `poetry run pre-commit` |
| Otherwise, `uv` installed | `uvx --from commitizen cz` | `uvx pre-commit` |
| Neither | Ask the user | Ask the user |

For Poetry, also run `poetry add --group dev commitizen "pre-commit@^4.0"`. Never raise the project's Python range to make this install work.

**Gate:** all 6 are answered and the runner is chosen.

**Only after Step 1**, continue.

## Step 2: Add the Commitizen config

Put this in `pyproject.toml` if it exists, otherwise in `.cz.toml`:

```toml
[tool.commitizen]
name = "cz_conventional_commits"
version = "<latest tag without v, or 0.1.0>"
tag_format = "v$version"
version_files = []  # one entry per file from Q3, e.g. "pyproject.toml:^version", "VERSION"
bump_message = "bump: version $current_version → $new_version"
update_changelog_on_bump = true
annotated_tag = true
```

**Gate:** `version` matches the latest tag and the project's files, and every file holding the version is listed.

## Step 3: Add the Makefile targets

Merge these in and keep existing targets. Recipes must use tabs. Set `CZ` and `PRE_COMMIT` from Step 1.

```make
.PHONY: setup-hooks check-clean fetch-tags bump push-tag

CZ ?= uvx --from commitizen cz
PRE_COMMIT ?= uvx pre-commit

setup-hooks:
	$(PRE_COMMIT) install --hook-type commit-msg

check-clean:
	@test -z "$$(git status --porcelain)" || (echo "Working tree is dirty: commit or stash first" && exit 1)

fetch-tags:
	git fetch --tags

# To force a level: make bump INCREMENT=PATCH (or MINOR, MAJOR)
bump: check-clean fetch-tags
	$(CZ) bump $(if $(INCREMENT),--increment $(INCREMENT))

push-tag: fetch-tags
	git push --follow-tags origin HEAD
```

With no remote, remove `fetch-tags` (including from `bump`) and `push-tag`, and say so.

## Step 4: Add the commit-msg hook

Add this to `.pre-commit-config.yaml`, then run `$(PRE_COMMIT) autoupdate --repo https://github.com/commitizen-tools/commitizen` and `make setup-hooks`:

```yaml
repos:
  - repo: https://github.com/commitizen-tools/commitizen
    rev: v3.25.0
    hooks:
      - id: commitizen
        stages: [commit-msg]
```

**Only after Steps 2-4**, continue.

## Step 5: Verify

Run all three with the chosen runner, and report the output:

1. `make -n bump` prints the expected recipe.
2. `$(CZ) check --message "bad message"` fails.
3. `$(CZ) bump --dry-run --yes` shows the next version. "No commits to bump" is expected right after a tag. With no commits at all it fails: skip this check and say so.

**Gate:** all three behave as described. Do not commit, tag, bump or push unless the user asks.

With no tags yet, `make bump` asks "Is this the first tag created?" and crashes without a terminal. Tell the user to make the first release with `$(CZ) bump --yes`; later releases use `make bump`.

## Anti-rationalization check

| Your thought | Reality |
|---|---|
| "`check-clean` is overkill" | It stops unrelated edits from reaching a release commit. Keep it. |
| "A real bump is the easiest test" | It creates a commit and a tag. Use `--dry-run`. |
