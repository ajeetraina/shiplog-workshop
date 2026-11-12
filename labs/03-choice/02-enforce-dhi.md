# Bake it into every sandbox

Offering works — but it still depends on the agent *asking*, and on *this* agent asking
*this* time. A new agent, a different prompt, a skipped MCP query, and `node:latest` creeps
back in.

Second approach: stop offering and start **baking**. Install DHI as a **kit** — part of the
sandbox image itself, so hardened bases are the default in every sandbox, for every agent, no
asking required.

```bash
sbx kit add dhi
```

With the kit installed, the hardened baseline is a property of the environment. An agent that
writes `FROM node:...` resolves against the hardened catalogue baked into the sandbox — there
is no unhardened base within reach to choose.

The Dockerfile already reflects the hardened base:

```bash
cat shiplog/Dockerfile
```

> `FROM dhi/node:22-hardened`

Same guarantee as the MCP approach — but now it holds whether or not anyone remembers to ask.
