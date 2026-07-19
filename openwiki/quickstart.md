# OpenWiki — case3-flesh-test

## Overview

This is a **minimal throwaway repository** created for the OpenWiki Stage 2 case-3 "细节补全隔离真跑" (detail-completion isolation test). It is not a production project or a teaching demo — it exists solely to exercise the OpenWiki documentation pipeline in an isolated environment.

The repository contains:

| Item | Path | Purpose |
|------|------|---------|
| Greeting function | `pkg/greet.py` | A single trivial function used as source material for the flesh-review test |
| CI workflow | `.github/workflows/openwiki-update.yml` | Scheduled GitHub Actions job that runs OpenWiki and opens a PR with updated docs |
| Agent instructions | `AGENTS.md`, `CLAUDE.md` | Standard OpenWiki boilerplate telling agents to start at this wiki |
| Project README | `README.md` | States this is a throwaway test repo |

## What This Repo Tests

The case-3 test validates that OpenWiki can:

1. **Initialize from a near-empty repo** — generate meaningful documentation even when there is very little source code.
2. **Handle isolation correctly** — confirm-1 (wiring from scratch) and confirm-9 (token-free visible comparison) scenarios are being tested.
3. **Run via CI** — the GitHub Actions workflow exercises the full `openwiki code --update --print` cycle and creates a pull request with the results.

## Source Code

### `pkg/greet.py`

The entire codebase is a single function:

```python
def greet(name: str) -> str:
    """返回一句问候语，供细节补全隔离测试用。"""
    return f"Hello, {name}!"
```

It takes a name string and returns a greeting. The docstring explicitly states it exists for the detail-completion isolation test.

## CI / Operations

The workflow at `.github/workflows/openwiki-update.yml` is the only operational surface:

- **Trigger**: Daily at 08:00 UTC (`cron: "0 8 * * *"`) or manual `workflow_dispatch`.
- **Runtime**: Ubuntu-latest, Node.js 22.
- **Steps**: Installs `openwiki` globally via npm, runs `openwiki code --update --print`, then uses `peter-evans/create-pull-request@v7` to open a PR on branch `openwiki/update`.
- **Provider**: OpenRouter (`OPENWIKI_PROVIDER=openrouter`) with model `z-ai/glm-5.2`.
- **Secrets required**: `OPENROUTER_API_KEY`, `LANGSMITH_API_KEY` (for tracing).
- **PR paths**: The workflow commits changes under `openwiki/`, `AGENTS.md`, `CLAUDE.md`, and the workflow file itself.

## macOS AppleDouble Files

The repository contains `._*` prefixed files (e.g. `._README.md`, `._greet.py`, `._.github`). These are macOS AppleDouble resource-fork artifacts created by the external SSD filesystem. They are **not source code** and should be ignored for all documentation and analysis purposes. Ideally they should be added to `.gitignore` or removed.

## Backlog

No deferred areas — the repository is fully documented above.
