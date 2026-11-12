# What just happened

> `agent`  ──[ placeholder ]──▶  `sbx proxy`  ──[ real token ]──▶  `api.github.com`

The real `GITHUB_TOKEN` **never enters the VM**. The sandbox injects a placeholder, and the
proxy swaps it for the real value *on the way out* — only for allowed destinations. So:

- A leaked log, a hostile dependency, or a prompt-injected agent dumping its whole
  environment all get a **useless placeholder**.
- The agent still did real work: the GitHub calls succeeded.
- A destructive command stays contained to a disposable VM; your host is untouched.

**That's Contain:** full access for the agent, a blast radius of zero for you.

Next, in **Control**, the agent installs dependencies — and one of them tries to phone home.
We'll decide exactly where the box is allowed to reach.
