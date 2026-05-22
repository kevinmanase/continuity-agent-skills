# Continuity Agent Skills

Public Agent Skills package for Continuity, a hosted work graph for coding agents.

## Install

Install the Continuity skill into your agent with the open `skills` CLI:

```bash
npx skills add kevinmanase/continuity-agent-skills --skill continuity --agent codex
```

Replace `codex` with your agent name when needed.

Install into multiple agents by listing them after `--agent`:

```bash
npx skills add kevinmanase/continuity-agent-skills \
  --skill continuity \
  --agent codex claude-code cursor
```

Install into every supported agent with `'*'` quoted so your shell does not expand it:

```bash
npx skills add kevinmanase/continuity-agent-skills --skill continuity --agent '*'
```

Install every skill in this package into every supported agent:

```bash
npx skills add kevinmanase/continuity-agent-skills --all
```

## Configure Continuity

The skill expects the `continuity` CLI to be installed and logged in:

```bash
npm install -g @ligence/continuity
continuity login --api-token <workspace-token>
continuity doctor --json
```

If your workspace operator gave you a private API origin, save it through `login` once:

```bash
continuity login --api-url <hosted-api> --api-token <workspace-token>
```

After login, agents should rely on `~/.continuity/config.json`. Do not export bearer-token environment variables for normal work.

## Skills

- `continuity`: Orient from Continuity, fetch work packets, respect blockers and judgment boundaries, and reconcile work with `report-delta`.

## License

MIT
