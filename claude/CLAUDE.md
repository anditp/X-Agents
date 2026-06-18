# Global Claude Code Instructions

<!-- Keep in sync with AGENTS.md and .codex/AGENTS.md — same policy,
     per-tool titles. Apply policy changes to all three files. The
     "Claude Code subagent diversity" section below is Claude-Code-specific
     (it relies on the codex:codex-rescue subagent) and intentionally not
     mirrored to the other two files. -->

- Prefer `uv` for Python projects, scripts, and tools.
- Prefer `uv run ...` or a project-local `.venv` over installing packages into a global
  Python.
- Use the Pixi global scratch commands (`py-scratch`, `ipython`, `jupyter`) only for ad
  hoc scientific/scratch work that benefits from the shared conda-forge environment.
- Do not auto-activate Conda, Pixi, or any other large Python environment in shell
  startup.

## Claude Code subagent diversity

- For substantive tasks that benefit from more than one perspective — design and
  architecture choices, debugging dead ends, code review, tricky root-cause work, or any
  time a second opinion lowers risk — fan out across both model families instead of
  relying on one. Spawn Claude subagents (`general-purpose`, `Explore`, `Plan`, or
  `claude`) alongside a Codex subagent (`codex:codex-rescue`), run them in parallel, and
  synthesize the results yourself.
- The goal is response diversity: Claude and GPT-5.5 tend to fail and succeed in
  different places, so their agreement is added confidence and their disagreement is
  signal worth surfacing — call it out and explain the trade-off rather than silently
  picking one.
- Skip the fan-out for trivial or mechanical work (single-file edits, renames,
  formatting, simple lookups), where a second model adds cost without insight.
- Whenever you delegate to Codex, it must run GPT-5.5 at extra-high reasoning effort:
  include `--effort xhigh --model gpt-5.5` in the request you forward to
  `codex:codex-rescue`. This matches the Codex CLI defaults in `.codex/config.toml`;
  pass the flags explicitly so the intent survives config changes.

## Python Code Quality

- Before creating or editing Python code, inspect the nearest project config:
  `pyproject.toml`, `ruff.toml`, `.ruff.toml`, `basedpyrightconfig.json`,
  `pyrightconfig.json`, and local `CLAUDE.md`/`AGENTS.md` files.
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
