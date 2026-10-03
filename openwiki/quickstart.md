---
type: Project
title: case3-flesh-test Quickstart
description: Minimal throwaway repository for OpenWiki Stage 2 case-3 isolated flesh-review test. Single greet function, a CI update workflow, and a macOS AppleDouble artifact caveat.
tags: [openwiki, flesh-review, isolated-test]
verified:
  - by: openwiki/0.7.0
    at: 2026-10-03T12:49:18.580Z
sources:
  - id: openwiki-source-f59d45dc30dfe454190bcd57
    resource: repo://._.github
  - id: openwiki-source-1f7ccc6f169cc445faedcbbe
    resource: repo://._README.md
  - id: openwiki-source-6d4b4e707b8d60b6ccfa3425
    resource: repo://.github/workflows/openwiki-update.yml
  - id: openwiki-source-8037e2358a2c4f9b2c722a11
    resource: repo://AGENTS.md
  - id: openwiki-source-a2371d6362e5db4bc834ad03
    resource: repo://CLAUDE.md
  - id: openwiki-source-5aab4b7d9b4c6fa297952f4b
    resource: repo://pkg/._greet.py
  - id: openwiki-source-5ab8e3d9302bf393b6f368ce
    resource: repo://pkg/greet.py
  - id: openwiki-source-23775c3de52f3ab95a13cb8b
    resource: repo://README.md
generated: { by: "openwiki/0.7.0", at: "2026-10-03T12:49:18.580Z" }
---

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

## Task Routing

This is the only wiki page — all routes terminate here, there are no child pages. The repository has insufficient coverage to warrant conceptual, architecture, workflow, operations, or testing subpages, so everything is documented below.

| If you need… | Section |
|--------------|--------|
| The source function (`greet`) | [Source Code](#source-code) |
| The CI / operations workflow | [CI / Operations](#ci--operations) |
| Why `._*` files exist and must be ignored | [macOS AppleDouble Files](#macos-appledouble-files) |
| Agent instruction files (not source) | [Agent Boilerplate](#agent-boilerplate) |

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

- **Signature**: `greet(name: str) -> str`.
- **Docstring purpose**: returns a greeting, for use in the detail-completion isolation test (供细节补全隔离测试用).
- **Behavior**: takes a name string and returns `Hello, {name}!`.
- There is **no caller**, **no state**, **no configuration**, and **no error handling** — the function stands alone.

## CI / Operations

The workflow at `.github/workflows/openwiki-update.yml` is the only operational surface:

- **Trigger**: Daily at 08:00 UTC (`cron: "0 8 * * *"`) or manual `workflow_dispatch`.
- **Runtime**: `ubuntu-latest`, Node.js 22.
- **Steps**: Checks out the repo, installs `openwiki` globally via npm, runs `openwiki code --update --print`, then uses `peter-evans/create-pull-request@v7` to open a PR on branch `openwiki/update`.
- **Provider/model**: OpenRouter (`OPENWIKI_PROVIDER: openrouter`) with model `z-ai/glm-5.2` (`OPENWIKI_MODEL_ID`).
- **Secrets required**: `OPENROUTER_API_KEY` and `LANGSMITH_API_KEY` (LangSmith tracing, with `LANGCHAIN_PROJECT: openwiki` and `LANGCHAIN_TRACING_V2: "true"`).
- **PR paths**: The workflow commits changes under `openwiki`, `AGENTS.md`, `CLAUDE.md`, and `.github/workflows/openwiki-update.yml` (via `add-paths`), with commit message and title `docs: update OpenWiki`.

## macOS AppleDouble Files

The repository contains `._*` prefixed files (e.g. `._README.md`, `._AGENTS.md`, `._CLAUDE.md`, `pkg/._greet.py`, `._.github`). These are macOS AppleDouble resource-fork artifacts created by the external SSD filesystem. They are **not source code** and must be ignored for all documentation and analysis purposes. Ideally they should be added to `.gitignore` or removed.

## Agent Boilerplate

`AGENTS.md` and `CLAUDE.md` are standard OpenWiki boilerplate — they are **not source code**. They instruct agents not to preload the wiki at task start, to use `openwiki_search` / `openwiki_read` for just-in-time retrieval when needed, and to fall back to `openwiki/quickstart.md` when the retrieval tools are unavailable. `CLAUDE.md` includes `AGENTS.md` by reference (`@AGENTS.md`), so the two carry the same instructions.

## Backlog

No deferred areas — the repository is fully documented above.
