# What just happened

You governed the agent's supply chain two ways, and the difference is the whole point:

- **MCP server** — the agent *asks* and is *offered* only hardened images **at decision
  time**. Governance by invitation: the menu is yours, but the agent still has to pick from
  it, every time.
- **Kit** — the hardened baseline is *baked into the sandbox* for **everyone**, no asking.
  Governance by default: it is a property of the environment, not a hope about each agent's
  behaviour.

> 🎙️ Supply-chain safety becomes a property of the environment — a choice you made *once*,
> not a choice you hope each agent makes correctly.

Either way, the result on the Dockerfile is the same:

> `FROM dhi/node:22-hardened`  —  0 known CVEs, non-root, minimal

The agent still moves fast. It simply **cannot** choose an unhardened base and ship 180 CVEs
to production.

**That's Choice:** you don't audit every decision the agent makes — you shape the set of
decisions it *can* make.

Next, in **Capacity**, we take everything so far — the box, the network policy, this hardened
kit — and declare it *once*, in the repo, so every developer and every agent gets the
byte-identical environment.
