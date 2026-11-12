# Try to leak the token

Now play the attacker. The agent is compromised and wants your `GITHUB_TOKEN`. Dump the
whole environment inside the box, the way a malicious dependency would:

```bash
sbx exec shiplog-dev -- env
```

Look at the value of `GITHUB_TOKEN`. It is **`sbx-proxy-placeholder-…`** — not your real
token. You can also open the credential file the agent was handed:

:filelink[shiplog/.env]{path="shiplog/.env"}

Same story: placeholders, not secrets. The agent made real, authenticated GitHub calls a
moment ago — yet the real values are nowhere inside the box to steal.
