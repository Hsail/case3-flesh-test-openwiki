---
type: Project
title: case3-flesh-test Quickstart
description: Minimal throwaway repository for OpenWiki Stage 2 case-3 isolated flesh-review test.
resource: /README.md
tags: [openwiki, flesh-review, isolated-test]
verified:
  - by: openwiki/0.7.2
    at: 2026-10-24T12:18:29.870Z
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
generated: { by: "openwiki/0.7.2", at: "2026-10-24T12:18:29.870Z" }
---

# OpenWiki — case3-flesh-test

## Overview

This is a **minimal throwaway repository** created for the OpenWiki Stage 2 case-3 "细节补全隔离真跑" (detail-completion isolation test). It is not a production project or a teaching demo — it exists solely to exercise the OpenWiki documentation pipeline in an isolated environment.

The repository contains:

| Item | Path | Purpose |
|------|------|---------|
| Greeting function | `pkg/greet.py` | A single trivial function used as source material for the flesh-review test |
| CI workflow | `.github/workflows/openwiki-update.yml` | Scheduled GitHub Actions job that runs OpenWiki and opens a PR with updated docs |
| Agent instructions | `AGENTS.md`, `CLAUDE.md` | Generated OpenWiki boilerplate (CLAUDE.md `@`-includes AGENTS.md) telling agents to start at this wiki |
| Project README | `README.md` | States this is a throwaway test repo |

## Task Routing

This is the **only wiki page**. There are no sibling pages to navigate to, so every question about this repository — source code, CI pipeline, agent boilerplate, or the AppleDouble noise — resolves here.

| If you are asking about… | See section below |
|--------------------------|-------------------|
| What the repository is for | [Overview](#overview) |
| The greeting source function | [Source Code](#source-code) |
| The CI refresh pipeline | [CI / Operations](#ci--operations) |
| Agent instruction boilerplate | [Agent Instructions](#agent-instructions) |
| Stray `._*` files | [macOS AppleDouble Files](#macos-appledouble-files) |

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

## Agent Instructions

`AGENTS.md` and `CLAUDE.md` are **generated OpenWiki boilerplate**, not hand-authored project guidance. `CLAUDE.md` consists solely of an `@AGENTS.md` include of the `AGENTS.md` content, so both files carry identical agent guidance.

The boilerplate tells agents that this repository has a generated `openwiki/` evidence index that is optional just-in-time context — not required startup reading. Agents should not enumerate or preload wikis at task start; instead they should use `openwiki_search` / `openwiki_read` retrieval tools only when an unfamiliar architecture or dependency materially affects the task, or when source inspection leaves an important uncertainty. If those tools are unavailable, agents fall back to reading `openwiki/quickstart.md` (this page) and following its links. Source code and tests are authoritative; brief unknowns and review items are verification gaps, not automatic requirements. The scheduled workflow regenerates the wiki, so generated OpenWiki pages should not be hand-edited unless explicitly asked.

## macOS AppleDouble Files

The repository contains `._*` prefixed entries (e.g. `._README.md`, `._AGENTS.md`, `._CLAUDE.md`, `._pkg`, `._.github`, `._.git`, `._openwiki`) — macOS AppleDouble resource-fork artifacts created by the external SSD filesystem. They mirror the real files/directories as metadata sidecars and are **not source code**; they should be ignored for all documentation and analysis purposes. Ideally they should be added to `.gitignore` or removed.

## Backlog

No deferred areas — the repository is fully documented above.
