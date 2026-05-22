---
name: continuity
description: Use when starting or resuming Continuity-tracked agent work, selecting next work, fetching agent context, finishing/blocking work, resolving review feedback, merging/pushing/rebasing PRs, making significant progress on a tracked node, or before a final response when code, PR state, merge state, blockers, or verification changed.
---

# Continuity Agent Workflow

Continuity is the hosted work graph for coding agents. It is the source of truth for next work, agent packets, blockers, human-judgment boundaries, verification guidance, and work-delta reconciliation.

Use the `continuity` CLI against the hosted API. Do not ask users for a private source checkout, local graph database, repo-local script, or workspace ID.

## Required CLI

If the `continuity` command is missing, ask the user to install it:

```bash
npm install -g @ligence/continuity
```

Save the workspace token once:

```bash
continuity login --api-token <workspace-token>
```

If the user has a private hosted origin, save it through login:

```bash
continuity login --api-url <hosted-api> --api-token <workspace-token>
```

After login succeeds, rely on `~/.continuity/config.json`. Do not tell agents or users to export bearer-token environment variables for normal work.

## Start Or Resume

At the start or resume of Continuity-tracked work, check identity, compatibility, readiness, and the next work slice:

```bash
continuity whoami --json
continuity version --json
continuity doctor --json
continuity next --agent codex --horizon overnight --compact --json
```

Use the current agent name instead of `codex` when running in another agent.

Read the returned `sequence` or `continuation` before choosing work. If `sequence[0]` names runnable work, fetch the packet before editing:

```bash
continuity agent-context --node <node-id> --agent codex --json
```

Follow the packet's authority boundary, stop conditions, verification guidance, and final-answer reconciliation instructions.

## Capture Work

Use `insert` when a user asks for new work, reports a bug, makes a durable decision, or provides an actionable task that should become part of the graph:

```bash
continuity insert --text "<work, decision, bug, or goal>" --json
```

Use `intake` only when the input should stay in a pending review queue instead of directly mutating graph work:

```bash
continuity intake --text "<observation or request>" --json
```

After inserting graph work, rerun:

```bash
continuity next --agent codex --horizon overnight --compact --json
```

## Report Deltas

When work completes, blocks, fails, or reaches a judgment boundary, report the outcome:

```bash
continuity report-delta \
  --node <node-id> \
  --kind completed \
  --summary "<what changed>" \
  --evidence "<proof>" \
  --json
```

Use `--kind completed` for finished work, `blocked` for unresolved blockers, `failed` for failed attempts, `learned` for durable learning, and `needs-judgment` when a human decision is required.

A node marked `completed` can still receive follow-up deltas, evidence, verification updates, merge provenance, or learned context. Do not treat completed status as a reason to skip reconciliation.

## Final Response Rule

Before the final response, check whether code, PR state, merge state, blockers, verification, or durable project knowledge changed since the last delta. If a known graph node was touched, run `report-delta` before answering. If no graph write was made, say why.

## Boundaries

Stop and report clearly if:

- the hosted API is unavailable
- identity or workspace access fails
- the graph runtime is not ready
- selected work requires human judgment
- verification cannot be made explicit
- a required external credential, workspace, or billing decision is missing
- token, budget, or runtime limits prevent safe progress

Do not continue behind a `needs-judgment` boundary unless the user explicitly answers it. Do not invent work when `next` returns no runnable sequence; read and report the `continuation` field.
