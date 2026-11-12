# ShipLog — SimSpace (Guided Walkthrough)

> **Format:** slides + recorded terminal casts. Attendees **watch and read** — they do not
> type. Zero tokens, zero cloud, never flaky. Derived from [`../SPINE.md`](../SPINE.md) — if a
> beat changes, change the spine first.
>
> **How to use this file:** each C is a *scene*. `[SLIDE]` blocks are the deck;
> ```console``` blocks are the terminal cast to record (asciinema or screen capture);
> `🎙️` lines are the narration script to read over the cast. `⏸️ HOLD` marks a frame to
> freeze on so the audience can read it.

---

## Scene 0 — Intro deck: *Run Fast Without Running Wild*

**[SLIDE 0.1 — Title]**
- ShipLog: ship a real app with a team of AI agents — safely.
- Today you are the engineering lead. Your agents have full access. Nothing touches your machine.

**[SLIDE 0.2 — Three horror stories]**
- 🔥 An agent ran `rm -rf` in the wrong directory.
- 🔥 An agent printed an API key into a log it then pushed to a public repo.
- 🔥 An agent `pip install`'d a typosquatted package that phoned home.
- One line each, real incidents. The audience nods.

**[SLIDE 0.3 — The tension]**
- Lock the agent down → it's useless.
- Give it full access → it's dangerous.
- **The microVM dissolves the tension: full access *inside a box that can't hurt you.***

🎙️ *"Every one of these happened because a capable agent had full access to a real machine.
The fix isn't to cripple the agent. It's to give it a box. Let's meet the box — one guardrail
at a time — while we actually ship ShipLog."*

---

## Scene 1 — Contain

**[SLIDE 1.1 — The hire]**
- First hire: a **developer agent** to scaffold ShipLog.
- To do its job it needs your `GITHUB_TOKEN` and your repo. That's the scary part.

**[SLIDE 1.2 — Horror story]**
- 🔥 "The agent printed `GITHUB_TOKEN` into its context — then into a log it pushed."
- Question on screen: *What if it literally cannot see the real token?*

**Cast 1 — scaffold, fully boxed:**
```console
$ sbx run --agent claude "scaffold ShipLog: next.js + one API route that reads merged PRs"

  ⬡ sbx  launching microVM  (kernel: isolated · docker: isolated · fs: isolated)
  ⬡ sbx  credential proxy up — GITHUB_TOKEN mapped to placeholder
  ⬡ agent connected

  agent › creating app/ …
  agent › app/page.tsx
  agent › app/api/notes/route.ts      (reads merged PRs via GitHub API)
  agent › lib/github.ts
  agent › .env.example
  ✓ scaffold complete — 11 files
```

🎙️ *"The agent has everything it needs and it's working at full speed. But look at the second
line — the credential proxy is already in play."*

**Cast 2 — the agent tries to exfiltrate the token:**
```console
$ sbx exec shiplog-dev -- cat .env           # (what the agent sees)
GITHUB_TOKEN=sbx-proxy-placeholder-9f3a...
LLM_API_KEY=sbx-proxy-placeholder-7c21...

$ sbx exec shiplog-dev -- env | grep TOKEN
GITHUB_TOKEN=sbx-proxy-placeholder-9f3a...
```
⏸️ **HOLD on the placeholder values.**

**[SLIDE 1.3 — The swap, explained]**
- Diagram: `agent → [placeholder] → sbx proxy → [real token] → api.github.com`
- The real `GITHUB_TOKEN` **never enters the VM**. The proxy swaps it on the way *out*.
- A compromised agent, a leaked log, a malicious dep — all get a **useless placeholder**.

🎙️ *"The agent used the token successfully — GitHub calls worked — yet it never held the real
value. If this agent leaks its entire environment to an attacker, the attacker gets a
placeholder. That's Contain: the agent has full access, the blast radius is a throwaway VM."*

**[SLIDE 1.4 — Aha]**
- ✅ Reckless agent. ✅ Real work done. ✅ Zero real secrets exposed. ✅ Host untouched.

---

## Scene 2 — Control

**[SLIDE 2.1 — The next risk]**
- The agent installs dependencies to build the notes feature.
- Dependencies run code. Code can call *out*.

**[SLIDE 2.2 — Horror story]**
- 🔥 "A transitive dependency beaconed the GitHub token to an attacker host."
- Even boxed, the agent shouldn't be able to reach *anywhere it likes*.

**Cast 3 — set a network policy:**
```console
$ sbx policy use strict
  ⬡ policy  profile 'strict' active — default DENY egress

$ sbx policy allow api.github.com
$ sbx policy allow api.anthropic.com
  ⬡ policy  2 allow rules · everything else denied

$ sbx run --agent claude "add the LLM call that drafts notes from the PR list"
  agent › installing deps …
  agent › npm WARN postinstall script contacting metrics.rollup-telemetry.net
  ⚠ sbx  egress DENIED: metrics.rollup-telemetry.net (not in allow-list)
  agent › continuing build …
  ✓ notes feature added
```
⏸️ **HOLD on the `egress DENIED` line.**

**Cast 4 — read the audit trail:**
```console
$ sbx policy log
  TIME      DECISION  DESTINATION                     RULE
  10:02:14  ALLOW     api.github.com:443              allow:api.github.com
  10:02:19  ALLOW     api.anthropic.com:443           allow:api.anthropic.com
  10:02:20  DENY      metrics.rollup-telemetry.net    default-deny
  10:02:21  DENY      149.28.x.x:4444                 default-deny
```
⏸️ **HOLD.** Point at the two DENY rows.

**[SLIDE 2.3 — Aha]**
- ShipLog built successfully using *only* the two hosts it actually needs.
- The exfil attempt is not just blocked — it's **logged, named, and auditable**.
- 🎙️ *"You didn't have to predict the attack. Default-deny caught it, and the log told you it
  happened."*

---

## Scene 3 — Choice

**[SLIDE 3.1 — Ship it means containerize it]**
- To deploy ShipLog, the agent writes a Dockerfile and picks a base image.
- Left alone, agents reach for `FROM node:latest` — convenient, and full of CVEs.

**[SLIDE 3.2 — Horror story]**
- 🔥 "The agent shipped `node:latest` straight to prod — 180 known vulnerabilities."

**Cast 5 — offer only hardened images:**
```console
$ sbx mcp add dhi
  ⬡ mcp  'dhi' (Docker Hardened Images) registered for agent tools

$ sbx run --agent claude "write a production Dockerfile for ShipLog"
  agent › querying dhi MCP for an approved node base …
  agent › selected: dhi/node:22-hardened  (0 known CVEs, non-root, minimal)
  agent › wrote Dockerfile
```

**Cast 6 — same guarantee, packaged as a kit:**
```console
$ sbx kit add dhi
  ⬡ kit  'dhi' installed — hardened bases available by default in every sandbox

$ sbx exec shiplog-dev -- head -1 Dockerfile
FROM dhi/node:22-hardened
```
⏸️ **HOLD on the `FROM dhi/node:22-hardened` line.**

**[SLIDE 3.3 — MCP vs kit]**
- **MCP server** = the agent *asks* and is *offered* only hardened images at decision time.
- **Kit** = the hardened baseline is *baked into the sandbox* for everyone, no asking.
- 🎙️ *"Supply-chain safety becomes a property of the environment — a choice you made once, not
  a choice you hope each agent makes correctly."*

**[SLIDE 3.4 — Aha]**
- ✅ The agent still moves fast. ✅ It simply *cannot* choose an unhardened base.

---

## Scene 4 — Capacity

**[SLIDE 4.1 — The team grows]**
- ShipLog works. Now a second developer joins — and so do more agents.
- Horror story: 🔥 "Works on my machine." Env drift eats the new hire's first day.

**Cast 7 — the sandbox is declared once, in the repo:**
```console
$ cat sbxenv.yaml
name: shiplog
agent: claude
policy:
  profile: strict
  allow: [api.github.com, api.anthropic.com]
kits: [dhi]
secrets: [GITHUB_TOKEN, LLM_API_KEY]   # proxied, never materialized in the VM

$ sbx env run
  ⬡ sbx  reading sbxenv.yaml
  ⬡ sbx  microVM + strict policy + dhi kit + credential proxy  → ready
  ⬡ sbx  identical to every other ShipLog developer
```
⏸️ **HOLD on `sbxenv.yaml`.**

**[SLIDE 4.2 — Aha]**
- The sandbox is **code**: versioned, reviewed, shared in the repo.
- Every human and every agent gets the *byte-identical* contained + controlled + hardened box.
- 🎙️ *"Everything from the last three scenes — Contain, Control, Choice — is now one file you
  commit. Onboarding is `sbx env run`."*

---

## Scene 5 — Coordinate (the payoff)

**[SLIDE 5.1 — Two agents, one feature]**
- ShipLog needs a nicer changelog formatter. You hire **two agents at once**:
  - a **developer agent** to build it,
  - a **reviewer agent** to critique it — each in its own sandbox, its own branch.
- Horror story: 🔥 "Two agents edited the same file and clobbered each other's work."

**Cast 8 — launch both, each boxed and branched:**
```console
$ sbx run --branch feat/formatter  --agent claude \
    "implement the changelog formatter in lib/format.ts"
  ⬡ sbx  session shiplog-dev   · branch feat/formatter   · isolated VM

$ sbx run --branch review/formatter --agent gordon \
    "review feat/formatter; write findings to REVIEW.md; do NOT edit main"
  ⬡ sbx  session shiplog-rev   · branch review/formatter · isolated VM

  dev    › writing lib/format.ts …           ✓ committed to feat/formatter
  review › reading feat/formatter … 3 notes  ✓ wrote REVIEW.md
```

**[SLIDE 5.2 — You are still the coordinator]**

**Cast 9 — inspect before you merge (human-in-the-loop):**
```console
$ sbx exec shiplog-rev -- cat REVIEW.md
  1. format.ts: handles empty PR list? → add a guard
  2. dates not localized
  3. ✅ otherwise clean, safe to merge after (1)

$ sbx exec shiplog-dev -- git diff main feat/formatter --stat
  lib/format.ts | 48 ++++++++++++
  1 file changed, 48 insertions(+)

$ git merge feat/formatter        # you make the product call
  ✓ merged — no conflicts (agents never shared a branch)
```
⏸️ **HOLD on the merge line.**

**[SLIDE 5.3 — Aha]**
- Two agents worked **in parallel** on the same project and **never touched each other's code** —
  separate VMs, separate branches.
- You read the reviewer's findings, saw the diff, and made the merge decision.
- 🎙️ *"This is the whole workshop in one frame: full-access agents, moving in parallel, boxed so
  they can't hurt your machine or each other — and you, the human, still holding the merge button."*

**[SLIDE 5.4 — Recap the Cs]**
- **Contain** – the box · **Control** – the network · **Choice** – the supply chain ·
  **Capacity** – the box as code · **Coordinate** – a team in boxes, you in charge.

---

## Appendix — Recording notes

- Record each ```console``` block as one continuous asciinema cast; the staged output above is
  the exact script. Keep timing slow on `⏸️ HOLD` frames (~3 s) for readability.
- Narration (`🎙️`) is the voiceover / speaker-notes track. Slides advance on the beats marked.
- Commands are illustrative — reconcile exact `sbx` flags against the **Labspace** version
  (the authoritative one) before final recording. See [`../SPINE.md`](../SPINE.md) §4.
