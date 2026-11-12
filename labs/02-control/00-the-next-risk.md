# Control

In **Contain** you boxed a full-access agent and made its secrets useless to leak. The agent
is still working on **$$app$$** — now it's building the release-notes feature, which means it
has to **install dependencies**.

That's the next risk. Dependencies run code: `postinstall` scripts, build hooks, native
add-ons. And code can call *out*.

> 🔥 **Horror story.** A transitive dependency — three levels down from anything anyone chose
> — fired a `postinstall` script that beaconed the project's `GITHUB_TOKEN` to an attacker
> host. The box kept it off the laptop, but the token still left the building.

Containing the agent isn't enough. Even inside the box, the agent — and everything its
dependencies drag in — should **not be able to reach anywhere it likes**.

So we stop trusting the network by default. The box gets a policy:

**Deny all egress. Then allow only the handful of hosts the work actually needs.**
