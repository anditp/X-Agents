# Global Codex Instructions

- Prefer `uv` for Python projects, scripts, and tools in this repo.
- Prefer `uv run ...` or a project-local `.venv` over installing packages into a global
  Python.
- Use the Pixi global scratch commands (`py-scratch`, `ipython`, `jupyter`) only for ad
  hoc scientific/scratch work that benefits from the shared conda-forge environment.
- Do not auto-activate Conda, Pixi, or any other large Python environment in shell
  startup.

## Python Code Quality

- Before creating or editing Python code, inspect the nearest project config:
  `pyproject.toml`, `ruff.toml`, `.ruff.toml`, `basedpyrightconfig.json`,
  `pyrightconfig.json`, and local `AGENTS.md` files.
- Treat project-local Ruff, Pyright/BasedPyright, ty, pytest, and package settings as
  binding. Prefer the project commands and dependency environment over global tools.
- When creating a new Python project, package, script collection, or generated code
  handoff from scratch, add project-local quality config by default unless the user
  explicitly asks for a throwaway script. Prefer a `pyproject.toml` with Ruff configured
  for strict linting, Ruff formatting, the appropriate Python target version, and a type
  checker such as BasedPyright/Pyright or ty.
- Write Python that is expected to pass strict Ruff-style checks by default: explicit
  imports, clear names, typed public functions, no unused code, no avoidable broad
  exceptions, no print/debug leftovers, no ad hoc path hacks, and formatter-compatible
  layout.
- After editing Python, run the relevant checks before declaring the work done. Prefer,
  in order:
  - `uv run ruff check .` and `uv run ruff format --check .` when Ruff is a project
    dependency or the project uses `uv`.
  - `ruff check .` and `ruff format --check .` only when that matches the local
    project/tooling setup.
  - `uv run basedpyright .`, `uv run pyright .`, or `uv run ty check .` when the project
    configures that checker or includes it in its dependencies.
  - The project test command, usually `uv run pytest`, when tests exist or the change
    affects behavior.
- If a check cannot be run because dependencies are missing, the environment is
  unavailable, or the project has no corresponding tool config, say that explicitly and
  report the residual risk.
