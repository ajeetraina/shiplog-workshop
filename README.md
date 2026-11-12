# ShipLog Workshop — SimSpace labs

Ship a real app — **ShipLog**, an AI release-notes generator — with a *team of AI agents*,
while learning Docker Sandboxes (`sbx`) through the **5 Cs**:
**Contain · Control · Choice · Capacity · Coordinate**.

These are **[SimSpace](https://github.com/dockersamples/simspace) labs** — fully in-browser,
with a *simulated* terminal. No real Docker, no cloud, no tokens, no agent cost: every command
is scripted, so the workshop runs identically for everyone with nothing to install. SimSpace
provides the whole UI (instructions + terminal + slide deck + catalog); we only author content.

## The entries

| Order | Entry | Kind | Teaches |
|------|-------|------|---------|
| 1 | `00-intro-slides` | slide deck | Run Fast Without Running Wild — the horror stories + the microVM |
| 2 | `01-contain` | lab | microVM isolation + the credential proxy |
| 3 | `02-control` | lab | network policy: default-deny, allow-list, audit log |
| 4 | `03-choice` | lab | Docker Hardened Images via MCP, then as a kit |
| 5 | `04-capacity` | lab | `sbxenv.yaml` — the sandbox as reproducible code |
| 6 | `05-coordinate` | lab | two agents in parallel, branch-isolated, human-in-the-loop |

## Author / preview locally

You only need Docker.

```bash
docker compose up dev              # live preview at http://localhost:5173
docker compose run --rm validate   # validate every entry (regenerates labs.json)
```

Edit files under [`labs/<id>/`](labs/) and refresh the browser:
`labspace.yaml` (config), `simulator.yaml` (what each command does), `*.md` (one per section/chapter).

## Deploy

GitHub Pages via GitHub Actions — [`.github/workflows/deploy.yml`](.github/workflows/deploy.yml)
validates and publishes on every push to `main` (Settings → Pages → Source: "GitHub Actions").

## Reference

- [`docs/SPINE.md`](docs/SPINE.md) — the source-of-truth narrative + 5-C mapping
- [`docs/walkthrough.md`](docs/walkthrough.md) — the scene-by-scene script the labs are built from
- [`docs/SIMSPACE-FORMAT.md`](docs/SIMSPACE-FORMAT.md) — SimSpace authoring cheat-sheet
- [`AGENTS.md`](AGENTS.md) — orientation for authoring this repo with an AI agent
