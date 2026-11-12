# What just happened

> `sbxenv.yaml`  ──commit──▶  repo  ──`sbx env run`──▶  byte-identical box for everyone

The sandbox is now **code**: versioned, reviewed in pull requests, and shared in the repo like
any other source file. Everything from the last three scenes collapses into one committed file:

- **Contain** — the proxied secrets (`GITHUB_TOKEN`, `LLM_API_KEY`), never materialized in the VM.
- **Control** — the `strict` policy and its `allow` list.
- **Choice** — the hardened `dhi` kit as the base.

Every human and every agent gets the *byte-identical* contained + controlled + hardened box.
Onboarding stops being a day of env drift and becomes a single command: `sbx env run`.

**That's Capacity:** the sandbox as code, so the whole team — people and agents — scales on the
exact same guarantees.

Next, in **Coordinate**, two agents share this one declared box to build a feature together.
