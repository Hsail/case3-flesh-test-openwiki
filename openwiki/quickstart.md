---
type: Project
title: case3-flesh-test Quickstart
description: Minimal throwaway repository for OpenWiki Stage 2 case-3 isolated flesh-review test.
resource: /README.md
tags: [openwiki, flesh-review, isolated-test]
verified:
  - by: openwiki/0.7.2
    at: 2026-10-31T13:59:07.731Z
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
generated: { by: "openwiki/0.7.2", at: "2026-10-31T13:59:07.731Z" }
---

# OpenWiki — case3-flesh-test

## Overview

This is a **minimal throwaway repository** created for the OpenWiki Stage 2 case-3 "细节补全隔离真跑" (detail-completion isolation test). It is not a production project or a teaching demo — it exists solely to exercise the OpenWiki documentation pipeline in an isolated environment.

The repository contains:

| Item | Path | Purpose |
|------|------|---------|
| Greeting function | `pkg/greet.py` | A single trivial function used as source material for the flesh-review test |
| CI workflow | `.github/workflows/openwiki-update.yml` | Scheduled GitHub Actions job that runs OpenWiki and opens a PR with updated docs |
| Agent instructions | `AGENTS.md`, `CLAUDE.md` | OpenWiki boilerplate telling agents to start at this wiki |
| Project README | `README.md` | States this is a throwaway test repo |

## What This Repo Tests

The case-3 test validates that OpenWiki can:

1. **Initialize from a near-empty repo** — generate meaningful documentation even when there is very little source code.
2. **Handle isolation correctly** — confirm-1 (wiring from scratch) and confirm-9 (token-free visible comparison) scenarios are being tested.
3. **Run via CI** — the GitHub Actions workflow exercises the full `openwiki code --update --print` cycle and creates a pull request with the results.

## Source Code

### `pkg/greet.py`

The entire codebase is a single function — there is no subsystem, concept, or architecture hierarchy to expand beneath it:

```python
def greet(name: str) -> str:
    """返回一句问候语，供细节补全隔离测试用。"""
    return f"Hello, {name}!"
```

It takes a name string and returns a greeting. The docstring explicitly states it exists for the detail-completion isolation test.

## Agent Instructions (AGENTS.md / CLAUDE.md)

`AGENTS.md` and `CLAUDE.md` are OpenWiki boilerplate, not project logic. `AGENTS.md` instructs agents to treat the generated `openwiki/` evidence index as optional just-in-time context rather than required startup reading, and to prefer source code and tests as authoritative. When OpenWiki retrieval tools are available it directs agents to `openwiki_search`, `openwiki_read`, and `openwiki_list_workspaces`/`openwiki_list_wikis` for on-demand context, falling back to reading this `openwiki/quickstart.md` when those tools are unavailable. `CLAUDE.md` simply references `AGENTS.md` (`@AGENTS.md`). Do not hand-edit generated OpenWiki pages unless explicitly asked; prefer updating source and letting the workflow regenerate the wiki.

## CI / Operations

The workflow at `.github/workflows/openwiki-update.yml` is the only operational surface in this repository — there are no further subsystems:

- **Trigger**: Daily at 08:00 UTC (`cron: "0 8 * * *"`) or manual `workflow_dispatch`.
- **Runtime**: `ubuntu-latest`, Node.js 22.
- **Steps**: Checks out the repo, installs `openwiki` globally via `npm install --global openwiki`, runs `openwiki code --update --print`, then uses `peter-evans/create-pull-request@v7` to open a PR on branch `openwiki/update`.
- **Provider**: OpenRouter (`OPENWIKI_PROVIDER=openrouter`) with model `OPENWIKI_MODEL_ID=z-ai/glm-5.2`.
- **Secrets required**: `OPENROUTER_API_KEY` (model access) and `LANGSMITH_API_KEY` (LangSmith tracing, with `LANGCHAIN_PROJECT=openwiki` and `LANGCHAIN_TRACING_V2=true`).
- **PR add-paths**: The workflow commits changes under `openwiki`, `AGENTS.md`, `CLAUDE.md`, and `.github/workflows/openwiki-update.yml`.

## macOS AppleDouble Files

The repository contains `._*` prefixed files (e.g. `._README.md`, `._greet.py`-style `._pkg`, `._openwiki`, `._.github`). These are macOS AppleDouble resource-fork artifacts created by the external SSD filesystem. They are **not source code** and should be ignored for all documentation and analysis purposes.

## Backlog

No deferred areas — the repository is fully documented on this single page.
