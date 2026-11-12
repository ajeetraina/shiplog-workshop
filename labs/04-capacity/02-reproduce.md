# Reproduce it anywhere

The manifest is in the repo, so reproducing the sandbox is a single command — on your laptop,
the new hire's laptop, CI, or an agent's workspace:

```bash
sbx env run
```

Watch what it does:

- `reading sbxenv.yaml` — the box comes from the committed file, not from memory or setup docs.
- `microVM + strict policy + dhi kit + credential proxy → ready` — Contain, Control and Choice
  all stand up together, from the one declaration.
- `identical to every other ShipLog developer` — not "close enough". The same box, every time.

There is no "works on my machine" left to drift. The onboarding instruction for every new
developer and every new agent is exactly this line.
