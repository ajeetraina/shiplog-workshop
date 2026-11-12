# ShipLog Workshop — Shared Spine (source of truth)

> This is the single source of truth. Both delivery versions — **SimSpace** (guided
> walkthrough: slides + recorded terminal casts, watch & read) and **Labspace** (live
> `sbx` hands-on) — derive from this file. If a beat, command, or "aha" moment changes,
> change it **here first**, then propagate to both versions.

---

## 1. The app: ShipLog

**ShipLog** is an AI release-notes generator. It reads a repo's merged pull requests and
drafts a changelog.

Deliberately workshop-sized:

| Piece | Tech | Why it's here |
|-------|------|---------------|
| Front end | Next.js, one page | Something to show |
| API route | One handler | Where the agent does real work |
| Data | SQLite / one Postgres table | A DB worth protecting |
| External call #1 | GitHub API (read PRs) | Needs a **`GITHUB_TOKEN`** — a secret |
| External call #2 | LLM endpoint (draft notes) | Needs an **LLM API key** — a second secret |

**Why ShipLog and not a chatbot:** the app *intrinsically* needs a GitHub token **plus**
an LLM key **plus** git access — the "lethal trifecta" (private data + untrusted content +
egress) is baked into the premise, not bolted on. Every horror story is therefore real to
the app, not contrived.

---

## 2. The narrative spine

> **You are the engineering lead.** ShipLog needs the most dangerous credential combination
> you can hand an agent: a token that reads your source, a key that can talk to the outside,
> and a shell that can run anything. You hire AI agents **one at a time**. Each agent is more
> capable — and more dangerous — than the last. **Each C is the guardrail that lets you say
> yes to the next hire.**

The arc is **cumulative**: by the final lab the attendee has a contained, network-governed,
supply-chain-hardened, reproducible sandbox — so running *two agents in parallel* is the
natural payoff, exactly like the "lead agent + two developers" finale that inspired this.

---

## 3. The C-mapping

The live workshop runs **five** C-labs behind an intro deck. Keep all five, or collapse to
four by **folding Capacity into Coordinate** ("reproduce the sandbox, then run a team in it").

| # | C | Cold-open horror story | Agent scenario | Sandbox feature | The "aha" |
|---|---|------------------------|----------------|-----------------|-----------|
| 0 | **Intro deck** — *Run Fast Without Running Wild* | Montage: agents that `rm -rf`'d hosts, leaked keys, ran unapproved commands | — | Why a microVM boundary exists | "Full access *and* safety is possible." |
| 1 | **Contain** | Agent prints `GITHUB_TOKEN` into its context, then into a log it pushes | Dev agent scaffolds ShipLog with full access; tries to read `.env`, git creds, host keys | microVM isolation + **credential proxy** (real token never enters the VM; placeholder swapped at the proxy) | Same reckless agent, blast radius = a throwaway VM |
| 2 | **Control** | A transitive dep beacons the GitHub token to an attacker host | Agent needs only `api.github.com` + the LLM endpoint to build the notes feature | **Network policy**: profile + allow/deny rules + **policy log** | ShipLog builds fine; the exfil attempt shows up **denied** in the audit log |
| 3 | **Choice** | Agent builds `FROM node:latest` and ships a CVE-ridden base to prod | Agent containerizes ShipLog and must choose a base image / pull a tool | **DHI** (Docker Hardened Images) served as an **MCP server**, then as a **kit** | The agent is *only offered* trusted, hardened images — supply chain is your choice, not the agent's |
| 4 | **Capacity** | New teammate: "works on my machine," env drift eats a day | Onboard a second developer to ShipLog | Declare the sandbox once in **`sbxenv.yaml`**, `sbx env run` → identical env for everyone | One file in the repo = byte-identical, policy'd sandbox for every dev and agent |
| 5 | **Coordinate** | Two agents edit the same changelog formatter and clobber each other | **Developer agent** + **reviewer agent** work the same ShipLog feature in parallel, each in its own sandbox on its own branch | Multi-agent parallelism + **branch/worktree isolation** + **human-in-the-loop** (`sbx exec`) | You stay the coordinator: review two branches, merge — agents never corrupt each other's code |

---

## 4. Canonical command surface (shared by both versions)

Both versions present the **same commands** so muscle memory transfers Sim → Lab. In SimSpace
these are *shown in a recorded cast*; in Labspace they are *typed for real*.

> Syntax below is illustrative and must be reconciled against the installed `sbx` version
> before recording/publishing. Treat the **live Labspace lab as authoritative** for exact flags.

```bash
# Setup (once)
brew install docker/tap/sbx
sbx login

# C1 Contain — run an agent fully boxed
sbx run --agent claude "scaffold ShipLog: next.js + one API route that reads merged PRs"
# credential proxy: real GITHUB_TOKEN never enters the VM

# C2 Control — govern egress
sbx policy use strict                      # pick a profile
sbx policy allow api.github.com            # only what ShipLog needs
sbx policy allow <llm-endpoint>
sbx policy log                             # read the audit trail (see the DENY)

# C3 Choice — govern the supply chain
sbx mcp add dhi                            # offer Docker Hardened Images via MCP
sbx kit add dhi                            # ...then as a kit
# agent can now only build on hardened bases

# C4 Capacity — reproduce the sandbox
#   sbxenv.yaml committed to the repo:
sbx env run                                # every dev/agent gets the identical sandbox

# C5 Coordinate — run a team
sbx run --branch feat/formatter --agent claude   "implement the changelog formatter"
sbx run --branch review/formatter --agent gordon "review the formatter branch; do not edit main"
sbx exec <session> -- git diff             # you, the coordinator, inspect before merge
```

---

## 5. How each version realizes a beat

| Beat | Labspace (live) | SimSpace (guided walkthrough) |
|------|-----------------|-------------------------------|
| Agent scaffolds app | Real agent writes real files in a real microVM | Recorded terminal cast of the same run; attendee reads the narration |
| Credential proxy hides token | Real: `echo $GITHUB_TOKEN` inside VM shows a placeholder | Cast shows the placeholder + a callout slide explaining the swap |
| Exfil blocked | Real DENY line in `sbx policy log` | Cast freezes on the DENY line; slide annotates it |
| DHI enforced | Real hardened image pulled | Cast + slide diagram of MCP/kit gating |
| Parallel agents | Two real sandboxes, two branches | Split-screen cast of both agents; slide shows the merge decision |

**Invariant:** the *story beats, the on-screen commands, and the "aha" moments are identical.*
Only the backend (real `sbx` vs recorded cast) differs.

---

## 6. Open decisions (track here)

- [ ] Keep 5 Cs, or collapse to 4 (fold Capacity → Coordinate)?
- [ ] Which agents in the live version? (Claude Code / Gordon / other)
- [ ] LLM endpoint used by ShipLog itself (for the release-notes feature)
- [ ] Exact `sbx` flag syntax — reconcile against installed version
- [ ] Do attendees get a pre-seeded ShipLog repo, or scaffold from empty?
