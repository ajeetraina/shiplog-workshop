# The team grows

$$app$$ works. The agent is boxed (**Contain**), its network is on a leash (**Control**),
and it builds on a hardened base image (**Choice**). Then the team grows: a second developer
joins, and so do more agents.

> 🔥 **Horror story.** *"Works on my machine."* The new hire spends their first day fighting
> env drift — a different Node, a missing kit, a policy nobody wrote down. The box that kept
> everyone safe lived only in your shell history.

A sandbox you set up by hand doesn't scale to a team. Everyone reinvents it slightly
differently, and the guarantees from the last three scenes quietly erode.

**What if the entire sandbox were a file in the repo — reviewed, versioned, and reproduced
byte-for-byte by every human and every agent?**

That file is `sbxenv.yaml`. Let's read it, then reproduce the box from it.
