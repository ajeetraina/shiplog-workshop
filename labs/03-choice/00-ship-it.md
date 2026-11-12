# Choice

**$$app$$** works. Now you want to ship it — and shipping means containerizing it. You hand
the agent the job: **write a production Dockerfile and pick a base image.**

That one decision — *which base image* — quietly sets the security posture of everything you
deploy. And left to its own devices, an agent reaches for the most convenient answer:

> `FROM node:latest`

Convenient. Familiar. And carrying a long tail of known CVEs — a fat base layer the agent
never audited and you never asked for.

> 🔥 **Horror story.** An agent shipped `node:latest` straight to production — **180 known
> vulnerabilities** riding along in the base layer, none of them introduced by a single line
> of the agent's own code.

Containing the agent (Scene 1) and controlling its network (Scene 2) don't help here: the bad
base image is a *choice the agent made*, inside the box, with full authority to make it.

The fix isn't to review every Dockerfile by hand. It's to **govern the choice** — so the only
bases the agent can reach are ones you've already blessed. We'll do it two ways.
