# Contain

You are the engineering lead on **$$app$$**, an AI release-notes generator. It reads a
repo's merged pull requests and drafts a changelog.

Your first hire is a **developer agent** to scaffold the project. To do its job it needs
your `GITHUB_TOKEN` (to read PRs) and your `LLM_API_KEY` (to draft notes) — the most
dangerous credential combination you can hand an agent.

> 🔥 **Horror story.** A coding agent printed `GITHUB_TOKEN` into its context window — then
> into a log file it helpfully committed and pushed. The token was public before anyone
> noticed.

The reflex is to lock the agent down. But a crippled agent is a useless agent. The real
question is:

**What if the agent simply _cannot see_ the real secret — even while using it successfully?**

That is what a Docker Sandbox does. Let's watch.
