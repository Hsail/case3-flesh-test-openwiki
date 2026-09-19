---
type: ci-workflow
title: OpenWiki Update CI Workflow
description: The repository's only real operational surface — the GitHub Actions workflow that runs the OpenWiki documentation pipeline and opens an update PR. Covers triggers, runner/Node config, the openwiki install+run steps, OpenRouter provider/model config, required secrets, the add-paths commit scope, and the openwiki/update PR branch.
tags: [ci, github-actions, openwiki, documentation, automation, pull-request]
verified:
  - by: openwiki/0.5.2
    at: 2026-09-19T12:08:05.732Z
sources:
  - id: openwiki-source-6d4b4e707b8d60b6ccfa3425
    resource: repo://.github/workflows/openwiki-update.yml
  - id: openwiki-source-8037e2358a2c4f9b2c722a11
    resource: repo://AGENTS.md
generated: { by: "openwiki/0.5.2", at: "2026-09-19T12:08:05.732Z" }
---

# OpenWiki Update CI Workflow

The repository has one changeable operational surface: the GitHub Actions workflow at `.github/workflows/openwiki-update.yml`. It runs the OpenWiki documentation pipeline on a schedule and opens a pull request with the regenerated pages. Everything documented here is the contract an agent must respect when changing CI behavior.

## What this workflow regenerates

The workflow does not only refresh the `openwiki/` evidence index. Its `add-paths` commit scope spans four targets, so a single run may modify:

- `openwiki/` — the generated wiki pages.
- `AGENTS.md` — the agent guidance file (its `<!-- OPENWIKI:START -->…<!-- OPENWIKI:END -->` block is regenerated).
- `CLAUDE.md` — the sibling agent guidance file.
- `.github/workflows/openwiki-update.yml` — the workflow file itself.

The last point is an invariant worth calling out explicitly: **self-modification of this workflow is in-scope for its own PRs.** An update run may rewrite the very workflow that produced it, so an agent editing this file is editing code that future runs can also edit. Treat the current file as the source of truth, not any single run's output.

## Triggers

The workflow is gated on two triggers and nothing else:

- `schedule` with cron `0 8 * * *` — a daily run at 08:00 UTC.
- `workflow_dispatch` — manual on-demand invocation from the Actions UI.

There are no `push`, `pull_request`, or path filters. It is purely a scheduled/operational pipeline; it never fires on ordinary commits.

## Runner and toolchain

The single `update` job runs on `ubuntu-latest` and pins the toolchain to Node 22 via `actions/setup-node@v4`. OpenWiki itself is installed globally from npm (`npm install --global openwiki`) each run rather than vendored in the repository, so the workflow always uses the latest published OpenWiki CLI. There is no lockfile or pinned OpenWiki version in this repo.

## Permissions invariant

The job declares job-level permissions:

```yaml
permissions:
  contents: write
  pull-requests: write
```

Both grants are required and must not be dropped. `contents: write` lets the final step create a commit on the PR branch; `pull-requests: write` lets it open the pull request. The `peter-evans/create-pull-request@v7` action will fail to produce a PR without `pull-requests: write`, and will fail to commit without `contents: write`. Because the repository's default `GITHUB_TOKEN` permissions could otherwise be read-only, these job-level grants are the load-bearing permission surface — any edit to this workflow that removes them breaks the pipeline.

## The OpenWiki run

The pipeline step that does the actual work is:

```yaml
- name: Run OpenWiki
  run: openwiki code --update --print
  env:
    OPENWIKI_PROVIDER: openrouter
    OPENROUTER_API_KEY: ${{ secrets.OPENROUTER_API_KEY }}
    OPENWIKI_MODEL_ID: z-ai/glm-5.2
    LANGSMITH_API_KEY: ${{ secrets.LANGSMITH_API_KEY }}
    LANGCHAIN_PROJECT: openwiki
    LANGCHAIN_TRACING_V2: "true"
```

This is the entire operational configuration. The pieces:

- **Provider/model**: `OPENWIKI_PROVIDER=openrouter` selects the OpenRouter provider; `OPENWIKI_MODEL_ID=z-ai/glm-5.2` selects the model. These are fixed in the workflow, not configurable per run.
- **Required secrets**: two repository secrets must exist for the run to function:
  - `OPENROUTER_API_KEY` — authenticates the OpenRouter LLM calls that generate the pages.
  - `LANGSMITH_API_KEY` — authenticates LangSmith tracing.
- **Tracing**: `LANGCHAIN_PROJECT=openwiki` and `LANGCHAIN_TRACING_V2="true"` enable LangSmith/LangChain v2 tracing of the generation run under the `openwiki` project. These are hard-coded env values, not secrets.

A run with a missing or empty `OPENROUTER_API_KEY` cannot generate content; a missing `LANGSMITH_API_KEY` degrades tracing but the values are still required by the workflow's environment contract.

## Call sequence

The job is a linear five-step sequence. Each step's output feeds the next: the checkout provides the tree, Node enables the CLI, the global install provides the `openwiki` binary, the run mutates files in the working tree, and the PR step commits exactly those mutations.

<!-- openwiki: mermaid parse failed and this diagram was converted to a text fence so it does not break rendering. Fix the diagram source and restore the mermaid fence. Parser error: Heuristic: an unescaped angle bracket inside a label breaks rendering; rephrase the label. -->
```text
flowchart TD
    A["actions/checkout@v4<br/>Check out repository"] --> B["actions/setup-node@v4<br/>Node 22"]
    B --> C["npm install --global openwiki<br/>Install OpenWiki CLI"]
    C --> D["openwiki code --update --print<br/>Run with OpenRouter + LangSmith env"]
    D --> E["peter-evans/create-pull-request@v7<br/>Commit add-paths to openwiki/update and open PR"]
```

*The OpenWiki update job is a linear pipeline: checkout, Node setup, global install, generate, then open PR.*

## The pull request

The final step uses `peter-evans/create-pull-request@v7` (pinned by commit SHA `22a9089034f40e5a961c8808d113e2c98fb63676`, tagged v7) to turn the working-tree changes into a PR:

```yaml
with:
  add-paths: |
    openwiki
    AGENTS.md
    CLAUDE.md
    .github/workflows/openwiki-update.yml
  branch: openwiki/update
  commit-message: "docs: update OpenWiki"
  title: "docs: update OpenWiki"
  body: |
    Automated OpenWiki documentation update.

    This PR was generated by the scheduled OpenWiki workflow.
```

Key properties of this step:

- **`add-paths`** restricts which working-tree changes become the commit. Only changes under the four listed paths are staged; unrelated edits (even if present in the runner's tree) are ignored. This is what makes the self-modification of the workflow file safe and scoped.
- **Branch**: `openwiki/update` — a single fixed branch. `create-pull-request` force-pushes to it, so successive runs overwrite the same branch rather than accumulating one PR per run. There is effectively one rolling update PR at a time.
- **Commit message and title** are both the literal string `docs: update OpenWiki`.
- **PR body** is a fixed two-line template.

If `openwiki code --update --print` produces no diff in any `add-paths` target, `create-pull-request` detects the empty diff and simply does not open a PR; the run succeeds without producing a pull request. This is the normal "nothing changed" outcome, not a failure.

## Operational consequences for agents

- **Don't drop permissions.** Removing `contents: write` or `pull-requests: write` silently breaks the PR step.
- **Don't widen `add-paths` casually.** It is the commit-scope guardrail. Adding a path means OpenWiki output or a hand edit there can be committed by the automated PR.
- **Self-modification is expected.** Editing `.github/workflows/openwiki-update.yml` via an OpenWiki PR is not circular in a dangerous sense — the running workflow is already checked out and pinned; the change takes effect on the next run. But it does mean diffs to this file can appear in its own PRs.
- **The model and provider are fixed here.** Changing which LLM generates the wiki means editing this file's `env`, not passing a flag.
- **OpenWiki is unpinned.** Because it is `npm install --global openwiki` with no version, a new OpenWiki release changes generation behavior without any change to this repository. An agent debugging a surprising diff should consider the OpenWiki version as a variable.
- **Tracing is on.** Every run is traced to the `openwiki` LangSmith project; debugging a generation run means reading those traces, not local logs.

## Relationship to agent guidance

`AGENTS.md` tells human and agent readers not to hand-edit generated `openwiki/` pages unless explicitly asked, and to prefer updating source code/docs and letting OpenWiki regenerate. This workflow is the regeneration mechanism that guidance assumes — it is the single thing that keeps the generated index from drifting from the source. The two files form the operational contract: `AGENTS.md` states the "don't hand-edit" rule, and this workflow is the sanctioned way to update what that rule protects.
