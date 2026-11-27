---
type: Project
title: case3-flesh-test Quickstart
description: Sole wiki page for a minimal throwaway OpenWiki Stage 2 case-3 isolation-test repo. Routes readers to the only real surfaces — the single-function source pkg/greet.py and the OpenWiki CI update workflow — and flags macOS AppleDouble artifacts to ignore.
tags: [openwiki, flesh-review, isolated-test, quickstart]
verified:
  - by: openwiki/0.9.0
    at: 2026-11-27T17:42:45.900Z
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
generated: { by: "openwiki/0.9.0", at: "2026-11-27T17:42:45.900Z" }
---

# OpenWiki — case3-flesh-test

This is the complete wiki for the repository: there is intentionally no
architecture/concepts/workflows hierarchy, because the repository has nothing to
document beyond what is listed below. No backlog or deferred areas exist.

## Where to Go

| Task | Read |
|------|------|
| Understand the entire codebase | [pkg/greet.py](../pkg/greet.py) — one trivial `greet(name)` function |
| Understand CI / how docs get updated | [.github/workflows/openwiki-update.yml](../.github/workflows/openwiki-update.yml) |
| Agent retrieval conventions | [AGENTS.md](../AGENTS.md) (CLAUDE.md just references it) |
| Project self-description | [README.md](../README.md) |

## What This Repo Is

A **minimal throwaway repository** created for the OpenWiki Stage 2 case-3
"细节补全隔离真跑" (detail-completion isolation live run) — per the README, it
covers confirm-1 (wiring from scratch) and confirm-9 (token-free visible
comparison). It is not a production or teaching project. The case-3 test
validates that OpenWiki can:

1. **Initialize from a near-empty repo** — generate meaningful documentation
   even with almost no source code.
2. **Handle isolation correctly** — the confirm-1 and confirm-9 scenarios.
3. **Run via CI** — the GitHub Actions workflow exercises the full
   `openwiki code --update --print` cycle and opens a PR with the results.

## Source Code

`pkg/greet.py` is the entire codebase:

```python
def greet(name: str) -> str:
    """返回一句问候语，供细节补全隔离测试用。"""
    return f"Hello, {name}!"
```

It takes a name and returns a greeting. The docstring states it exists for the
detail-completion isolation test. That is all there is — do not look for further
modules, callers, or tests.

## CI / Operations

The workflow at `.github/workflows/openwiki-update.yml` is the only operational
surface:

- **Trigger**: daily at 08:00 UTC (`cron: "0 8 * * *"`) or manual
  `workflow_dispatch`.
- **Runtime**: `ubuntu-latest`, Node.js 22; needs `contents: write` and
  `pull-requests: write` permissions.
- **Steps**: check out the repo, install `openwiki` globally via npm, run
  `openwiki code --update --print`, then use
  `peter-evans/create-pull-request@v7` (pinned by commit SHA) to open a PR on
  branch `openwiki/update` titled "docs: update OpenWiki".
- **Provider/model**: `OPENWIKI_PROVIDER=openrouter`, model
  `z-ai/glm-5.2`; LangSmith tracing enabled (`LANGCHAIN_PROJECT=openwiki`,
  `LANGCHAIN_TRACING_V2=true`).
- **Secrets required**: `OPENROUTER_API_KEY`, `LANGSMITH_API_KEY`.
- **PR paths**: the PR commits changes under `openwiki/`, `AGENTS.md`,
  `CLAUDE.md`, and the workflow file itself.

## macOS AppleDouble Files

The repository contains `._*`-prefixed entries (`._README.md`, `._greet.py`,
`._.github`, `._openwiki`, `._AGENTS.md`, `._CLAUDE.md`, `._pkg`). These are
macOS AppleDouble resource-fork artifacts from the external SSD filesystem —
**not source**. Ignore them for all documentation and analysis purposes.

## Agent Instructions

[AGENTS.md](../AGENTS.md) and [CLAUDE.md](../CLAUDE.md) are standard OpenWiki
boilerplate, not bespoke project guidance: they tell agents to prefer just-in-time
retrieval (`openwiki_search` / `openwiki_read`) and, if the retrieval tools are
unavailable, to fall back to this quickstart page. CLAUDE.md contains only a
reference to AGENTS.md.
