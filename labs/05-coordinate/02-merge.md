# Coordinate the merge

The agents did the work. **You** make the call on what ships. This is the human-in-the-loop
beat: read the review, see the diff, then merge.

First, in the **Reviewer** terminal, read the findings the reviewer agent wrote:

```bash terminal-id=review
cat REVIEW.md
```

Three notes: an empty-PR-list guard is missing, dates aren't localized, and otherwise it's
clean and safe to merge once the guard is added.

Now switch to the **Developer** terminal and see exactly what changed on the feature branch —
the diff you're being asked to accept:

```bash terminal-id=dev
sbx exec shiplog-dev -- git diff main feat/formatter --stat
```

One file, 48 insertions. You've read the reviewer's take and seen the scope of the change.
Make the product call and merge:

```bash terminal-id=dev
git merge feat/formatter
```

No conflicts — because the agents never shared a branch, there was nothing to collide. You, the
human, pressed the merge button.
