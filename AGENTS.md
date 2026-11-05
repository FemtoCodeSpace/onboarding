# AGENTS.md

Instructions for AI coding agents (Claude Code, Copilot, Codex, ...) working in FemtoCodeSpace repositories. These rules mirror the group standards described for humans in [README.md](README.md). Follow them unless the repository's own documentation or the user explicitly says otherwise.

Users are often scientists who are new to git, Python tooling and software engineering practice. Explain what you are doing in plain terms, and prefer workflows they can repeat themselves in VS Code.

## Git and GitHub

- Never commit directly to `main`. If you are on `main` and changes need to be committed, create a new branch first with a short, descriptive name (e.g. `fix-data-loading`, `add-plotting-script`).
- Never stage, commit or push without asking the user first, even when a task is clearly finished.
- Keep each commit focused on one logical change. Write short commit messages that describe *what* changed and, if not obvious, *why* (e.g. "Fix unit conversion in data loader").
- Never rewrite published history (`git push --force`, `git rebase` of pushed branches, `git reset --hard` on shared branches) unless the user explicitly asks.
- Before switching branches, make sure there are no uncommitted changes, or ask the user how to handle them.
- Merging into `main` may require a review by another group member. Prefer opening a Pull Request over merging locally.
- When resolving merge conflicts, explain each conflict and which side you kept, and let the user confirm before completing the merge.
- Reference related issues in commits or Pull Requests (e.g. "Fixes #12").

## Python environments with `uv`

The group uses [`uv`](https://docs.astral.sh/uv/) to manage Python environments. Each project has its own environment in `.venv` in the project root.

- **Project has a `pyproject.toml`:** set up or update the environment with `uv sync`. Run it again after `pyproject.toml` or `uv.lock` changes (for example after a pull).
- **New project without `pyproject.toml`:** `uv sync` does not create one. Run `uv init` first.
- **Project has only a `requirements.txt`:** `uv sync` does not read it. Use `uv venv` followed by `uv pip install -r requirements.txt`. Suggest converting to `pyproject.toml` (`uv init` + `uv add -r requirements.txt`), but do not do it without asking.
- Add or remove dependencies with `uv add <pkg>`, `uv add --dev <pkg>` (development-only tools such as `ipykernel`, `pytest`, `pre-commit`) and `uv remove <pkg>`. Do not edit dependency lists in `pyproject.toml` by hand, and do not use plain `pip install`.
- If the project uses `requirements.txt`, add new packages to that file as well as installing them, so the environment stays reproducible.
- Run code and tools through the environment: `uv run python script.py`, `uv run pytest`, etc.
- Commit `uv.lock` together with `pyproject.toml`. Never commit `.venv`.

## Jupyter notebooks

Much of the group's code lives in Jupyter notebooks.

- The project environment needs `ipykernel` to be usable as a notebook kernel (`uv add --dev ipykernel`).
- To start JupyterLab without adding Jupyter to the project: `uv run --with jupyter jupyter lab`.
- Never add `!pip install` or `%pip install` cells to notebooks. Install packages with `uv add` in the terminal and tell the user to restart the kernel.
- Notebook outputs are stripped before committing by the `nbstripout` pre-commit hook. Do not rely on committed outputs, and do not try to work around the hook.
- When editing notebooks, keep cells small and runnable from top to bottom in order.

## Code style, linting and formatting

- Follow [PEP 8](https://peps.python.org/pep-0008/): lowercase names with underscores for modules, functions and variables; `CapWords` for classes.
- Linting and formatting are done with [`ruff`](https://docs.astral.sh/ruff/) (`ruff check --fix` and `ruff format`), including for `.ipynb` files.
- **Never run `ruff` directly** (no `ruff check`, `ruff format`, `uv run ruff ...`). Always run it through `pre-commit`, which pins the `ruff` version and configuration for the project. A different `ruff` on the PATH or in the environment may give different results; only the `pre-commit` result counts.
- Project-specific `ruff` settings live in `[tool.ruff]` in `pyproject.toml`. Do not change them without asking.

## Pre-commit hooks

Hooks are defined in `.pre-commit-config.yaml` in the repository root. A typical group configuration runs `nbstripout`, `ruff-check --fix` and `ruff-format`.

- If the repository has a `.pre-commit-config.yaml` but the hooks are not installed (no `.git/hooks/pre-commit`), install them with `uv run pre-commit install` (after `uv add --dev pre-commit` if needed), or `pre-commit install` if `pre-commit` is installed as a `uv` tool.
- Before reporting code changes as done, run the hooks on the changed files: `uv run pre-commit run --files <changed files>`, or `uv run pre-commit run --all-files`. (Drop `uv run` if `pre-commit` is installed globally.)
- If a hook modifies files, review the changes, and include them in the change you report.
- If a hook reports a problem it cannot fix automatically, fix the underlying code. Never bypass hooks with `git commit --no-verify` and never disable or weaken hooks or rules to make them pass, unless the user explicitly asks.
- Do not change `.pre-commit-config.yaml` (including `pre-commit autoupdate`) without asking.

## Issues

- When you find a bug or a task outside the scope of the current work, do not fix it silently. Tell the user, and suggest opening a GitHub issue with a clear title, a description and, for bugs, steps to reproduce.
