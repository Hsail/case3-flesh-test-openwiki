---
type: Project
title: case3-flesh-test Quickstart
description: Minimal throwaway repository for OpenWiki Stage 2 case-3 isolated detail-completion flesh-review test.
resource: /README.md
tags: [openwiki, flesh-review, isolated-test]
verified:
  - by: openwiki/0.5.2
    at: 2026-09-19T12:08:05.732Z
sources:
  - id: openwiki-source-6d4b4e707b8d60b6ccfa3425
    resource: repo://.github/workflows/openwiki-update.yml
  - id: openwiki-source-8037e2358a2c4f9b2c722a11
    resource: repo://AGENTS.md
  - id: openwiki-source-5ab8e3d9302bf393b6f368ce
    resource: repo://pkg/greet.py
  - id: openwiki-source-23775c3de52f3ab95a13cb8b
    resource: repo://README.md
generated: { by: "openwiki/0.5.2", at: "2026-09-19T12:08:05.732Z" }
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

## What This Repo Tests

The case-3 test validates that OpenWiki can:

1. **Initialize from a near-empty repo** — generate meaningful documentation even when there is very little source code.
2. **Handle isolation correctly** — the `confirm-1` (从零接线, wiring from scratch) and `confirm-9` (免 token 可见对照, token-free visible comparison) scenarios are being exercised.
3. **Run via CI** — the GitHub Actions workflow runs the full `openwiki code --update --print` cycle and creates a pull request with the results.

## Source Code

### `pkg/greet.py`

The entire codebase is a single function:

```python
def greet(name: str) -> str:
    """返回一句问候语，供细节补全隔离测试用。"""
    return f"Hello, {name}!"
```

It takes a name string and returns a greeting. The docstring explicitly states it exists for the detail-completion isolation test. There is no test suite — this one fixture is the only source surface.

## CI / Operations

The workflow at `.github/workflows/openwiki-update.yml` is the repository's **only real changeable operational surface**: a scheduled Ubuntu/Node 22 job that installs `openwiki`, runs `openwiki code --update --print`, and opens a PR on the `openwiki/update` branch via `peter-evans/create-pull-request@v7`.

➡️ **For the full operational reference — trigger schedule, runner/Node version, install+run steps, OpenRouter provider/model config, required secrets, and the `add-paths` commit scope — see [OpenWiki Update CI Workflow](/openwiki/operations/openwiki-update-workflow.md).**

## macOS AppleDouble Files

The repository contains `._*` prefixed files (e.g. `._README.md`, `._greet.py`, `._.github`, `._openwiki`). These are macOS AppleDouble resource-fork artifacts created by the external SSD filesystem. They are **not source code, tests, or documentation** and must be ignored for all analysis purposes. They should not be read, edited, or treated as authoritative content; ideally they would be added to `.gitignore` or removed.

## Backlog

No deferred areas — with only a one-line fixture and one CI workflow, the repository is fully documented above. Do not invent depth the repo does not have.
