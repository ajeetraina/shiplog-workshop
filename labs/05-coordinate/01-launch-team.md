# Launch the team

Two agents, two branches, two isolated VMs — kicked off in parallel.

In the **Developer** terminal, hire the developer agent on its own branch:

```bash terminal-id=dev
sbx run --branch feat/formatter --agent claude "implement the changelog formatter in lib/format.ts"
```

It spins up `session shiplog-dev` on `feat/formatter`, writes `lib/format.ts`, and commits —
all inside its own VM.

Now, in the **Reviewer** terminal, hire the reviewer agent on a _different_ branch. Its job is
to critique, not to change `main`:

```bash terminal-id=review
sbx run --branch review/formatter --agent gordon "review feat/formatter; write findings to REVIEW.md; do NOT edit main"
```

It opens `session shiplog-rev` on `review/formatter`, reads the developer's branch, and writes
up what it found.

Notice what did **not** happen: neither agent waited for the other, and neither could overwrite
the other's files. Separate VMs, separate branches — parallelism with zero clobbering.
