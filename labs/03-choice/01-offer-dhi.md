# Offer only hardened images

First approach: **offer** the agent a vetted catalogue and let it choose from that. Register
**Docker Hardened Images (DHI)** as an MCP server — a tool the agent can query at decision
time for an approved base.

```bash
sbx mcp add dhi
```

The agent now has a new tool: *"ask DHI for an approved node base."* It is only ever *offered*
hardened images — it still makes the call, but the menu it chooses from is yours.

Now ask it to write the Dockerfile:

```bash
sbx run --agent claude "write a production Dockerfile for ShipLog"
```

Watch the middle of the output. Instead of defaulting to `node:latest`, the agent queries the
DHI MCP and selects:

> `dhi/node:22-hardened`  —  0 known CVEs, non-root, minimal

See the file it wrote:

```bash
cat shiplog/Dockerfile
```

The first line is `FROM dhi/node:22-hardened`. The agent moved just as fast — it simply picked
from a better menu.
