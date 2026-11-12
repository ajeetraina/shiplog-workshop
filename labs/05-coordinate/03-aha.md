# What just happened

> `claude` → VM · `feat/formatter`  ───▶  **you** ◀───  `review/formatter` · VM ← `gordon`

Two agents worked **in parallel** on the same project and **never touched each other's code** —
separate VMs, separate branches. You read the reviewer's findings, saw the diff, and made the
merge decision yourself.

- **Parallel.** The developer and the reviewer ran at the same time. No careful single-file
  line, no waiting.
- **Isolated.** Each agent had its own microVM and its own branch. There was nothing to clobber
  and nothing to collide at merge time.
- **Human holds the merge button.** The agents proposed; you disposed. The product call stayed
  with you.

This is the whole workshop in one frame: full-access agents, moving in parallel, boxed so they
can't hurt your machine or each other — and you still holding the merge button.

## The five Cs

- **Contain** — the box: full access for the agent, a blast radius of zero for you.
- **Control** — the network: default-deny egress, every exfil attempt logged and named.
- **Choice** — the supply chain: only hardened base images, baked into the environment.
- **Capacity** — the box as code: one file, committed, identical for every human and agent.
- **Coordinate** — a team in boxes, you in charge.
