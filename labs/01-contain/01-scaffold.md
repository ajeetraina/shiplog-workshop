# Scaffold, fully boxed

Hire the agent and let it scaffold $$app$$ — inside a microVM with its own kernel, its own
Docker daemon, and an isolated filesystem.

```bash
sbx run --agent claude "scaffold ShipLog: next.js + one API route that reads merged PRs"
```

Watch the output. Two lines matter:

- `launching microVM` — the agent runs in a throwaway VM, **not on your machine**.
- `credential proxy up — GITHUB_TOKEN mapped to placeholder` — the proxy is already between
  the agent and your real secret.

The agent has everything it needs and is working at full speed. See the files it created:

```bash
ls shiplog
```

Nothing here touched your host. If this agent ran `rm -rf /`, it would erase a VM you throw
away — not your laptop.
