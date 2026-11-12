# Read the sandbox definition

The whole sandbox is declared once, in a single file committed to the repo. Open it:

```bash
cat sbxenv.yaml
```

Four lines carry the lessons of the last three scenes:

- `policy.profile: strict` with an `allow` list — that's **Control**: the box only reaches
  `api.github.com` and `api.anthropic.com`.
- `kits: [dhi]` — that's **Choice**: build on the hardened Docker Hardened Image base.
- `secrets: [GITHUB_TOKEN, LLM_API_KEY]` — that's **Contain**: names only. The proxy resolves
  them on egress; the real values are **never materialized in the VM**.
- `agent: claude` — who runs inside the box.

Now let `sbx` resolve that manifest and pretty-print the sandbox it describes:

```bash
sbx env show
```

One file, fully resolved: contained + controlled + hardened. Nothing lives in a shell history.
