# Set a network policy

Switch the sandbox to the `strict` profile. This flips egress to **default-deny**: nothing
leaves the box unless you say so.

```bash
sbx policy use strict
```

Now add back only what the build genuinely needs. ShipLog reads merged PRs from GitHub and
drafts notes via the LLM — two hosts, nothing more:

```bash
sbx policy allow api.github.com
```

```bash
sbx policy allow api.anthropic.com
```

That's the whole policy: two allow rules, and a wall around everything else. You didn't have
to predict the attack or enumerate the bad hosts — you only named the *good* ones. Anything a
dependency tries to reach that isn't on this short list is denied before a packet leaves.

Next, let the agent build — and watch default-deny do its job.
