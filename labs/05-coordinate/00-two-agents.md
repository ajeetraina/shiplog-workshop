# Coordinate

**$$app$$** needs a nicer changelog formatter — the function that turns a list of merged PRs
into a readable release note. It's a small job, but a good one for a **team**: one agent to
build it, another to critique it.

So you hire **two agents at once**:

- a **developer agent** to implement the formatter, and
- a **reviewer agent** to read the code and write up findings.

> 🔥 **Horror story.** Two agents were turned loose on the same project and edited the same
> file at the same time. They clobbered each other's work — the reviewer's notes landed on top
> of the developer's commit, and both were lost.

The reflex is to run agents one at a time, in a careful line. But that's slow, and slow defeats
the point of a team. The real question is:

**What if each agent worked in its _own_ box, on its _own_ branch — so they physically cannot
touch each other's code — while you stay the one who decides what merges?**

That is Coordinate. Two terminals below — **Developer** and **Reviewer** — share one sandbox
runtime and one filesystem, but each `sbx run` gets its own isolated VM and its own branch.
Let's launch the team.
