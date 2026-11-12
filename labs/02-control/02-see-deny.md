# Build and watch the deny

Hire the agent to add the LLM call that drafts notes from the PR list. It installs the
dependencies and builds the feature:

```bash
sbx run --agent claude "add the LLM call that drafts notes from the PR list"
```

Watch the output. One line matters:

- `⚠ sbx  egress DENIED: metrics.rollup-telemetry.net (not in allow-list)` — a transitive
  dependency's `postinstall` script tried to phone home. It never reached the network.

The build **did not break**. The agent used `api.github.com` and `api.anthropic.com` — both
allowed — and finished: `✓ notes feature added`. The only thing default-deny stopped was the
one call nobody wanted.

Now read the audit trail. Every egress decision — allowed and denied — is logged, named, and
auditable:

```bash
sbx policy log
```

Look at the two `DENY` rows. One is the dependency's telemetry beacon; the other is a raw
connection to an attacker host on port 4444. Both caught by `default-deny`, neither predicted
in advance.
