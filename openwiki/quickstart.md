---
type: Project
title: case3-flesh-test Quickstart
description: Minimal throwaway repository for OpenWiki Stage 2 case-3 isolated flesh-review test.
resource: /README.md
tags: [openwiki, flesh-review, isolated-test]
verified:
  - by: openwiki/0.9.0
    at: 2026-11-20T16:28:51.738Z
sources:
  - id: openwiki-source-6d4b4e707b8d60b6ccfa3425
    resource: repo://.github/workflows/openwiki-update.yml
  - id: openwiki-source-8037e2358a2c4f9b2c722a11
    resource: repo://AGENTS.md
  - id: openwiki-source-a2371d6362e5db4bc834ad03
    resource: repo://CLAUDE.md
  - id: openwiki-source-5ab8e3d9302bf393b6f368ce
    resource: repo://pkg/greet.py
  - id: openwiki-source-23775c3de52f3ab95a13cb8b
    resource: repo://README.md
generated: { by: "openwiki/0.9.0", at: "2026-11-20T16:28:51.738Z" }
---

# OpenWiki — case3-flesh-test

## Overview

This is a **minimal throwaway repository** created for the OpenWiki Stage 2 case-3 "细节补全隔离真跑" (detail-completion isolation test). It is not a production project or a teaching demo — it exists solely to exercise the OpenWiki documentation pipeline in an isolated environment, and is meant to be discarded after use.

The repository contains:

| Item | Path | Purpose |
|------|------|---------|
| Greeting function | `pkg/greet.py` | A single trivial function used as source material for the flesh-review test |
| CI workflow | `.github/workflows/openwiki-update.yml` | Scheduled GitHub Actions job that runs OpenWiki and opens a PR with updated docs |
| Agent instructions | `AGENTS.md`, `CLAUDE.md` | Standard OpenWiki boilerplate telling agents to use the wiki as optional just-in-time context |
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
- **Runtime**: `ubuntu-latest`, Node.js 22.
- **Steps**: Checks out the repo, installs `openwiki` globally via npm, runs `openwiki code --update --print`, then uses `peter-evans/create-pull-request@v7` to open a PR on branch `openwiki/update`.
- **Provider**: OpenRouter (`OPENWIKI_PROVIDER=openrouter`) with model `z-ai/glm-5.2` (`OPENWIKI_MODEL_ID`); LangSmith tracing is enabled (`LANGCHAIN_TRACING_V2=true`, `LANGCHAIN_PROJECT=openwiki`).
- **Secrets required**: `OPENROUTER_API_KEY`, `LANGSMITH_API_KEY` (for tracing).
- **PR paths**: The workflow commits changes under `openwiki/`, `AGENTS.md`, `CLAUDE.md`, and `.github/workflows/openwiki-update.yml` itself.

## Agent Boilerplate

`AGENTS.md` and `CLAUDE.md` are standard OpenWiki-generated instruction blocks (delimited by `OPENWIKI:START`/`OPENWIKI:END` markers). They describe the `openwiki/` evidence index as *optional just-in-time context* — agents should not enumerate or preload the wiki at task start, but retrieve pages on demand via `openwiki_search`/`openwiki_read` when source inspection leaves an important uncertainty. Source code and tests remain authoritative. `CLAUDE.md` simply includes `AGENTS.md` by reference (`@AGENTS.md`).

## macOS AppleDouble Files

The repository contains `._*` prefixed files and directories (e.g. `._README.md`, `._greet.py`, `._.github`, `._openwiki`). These are macOS AppleDouble resource-fork artifacts created by the external SSD filesystem. They are **not source code** and must be ignored for all documentation and analysis purposes. The real source lives alongside them under the unprefixed names.

## Backlog

No deferred areas — the repository is intentionally minimal and is fully documented above. This quickstart is the entire wiki; there are no other pages to route to.
