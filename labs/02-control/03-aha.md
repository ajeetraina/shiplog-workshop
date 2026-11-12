# What just happened

> `agent + deps`  ──▶  `sbx policy [ default-deny + allow-list ]`  ──▶  `api.github.com` ✅ · `metrics.rollup-telemetry.net` ⛔

ShipLog built successfully using **only the two hosts it actually needs**. Everything else —
including a dependency's attempt to beacon out and a raw connection to an attacker on port
4444 — was denied before a packet left the box. So:

- You didn't have to **predict the attack**. Default-deny caught a call nobody anticipated.
- The build **still worked**. Legitimate egress to allowed hosts went through at full speed.
- The exfil attempt isn't just blocked — it's **logged, named, and auditable**. The policy
  log told you it happened, when, and which rule stopped it.

**That's Control:** the box can only reach where you allowed, and every attempt to reach
elsewhere is a record you can read.

Next, in **Choice**, the agent has to pick a base image to containerize $$app$$ — and left
alone, agents reach for `node:latest`. We'll make the safe choice the only choice.
