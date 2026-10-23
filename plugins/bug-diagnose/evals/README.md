# Backtest

A diagnosis only means something if it was produced without the answer. Run the skill on bugs that are already fixed, in backtest mode, and compare its output to the real fix.

## Run one case

1. Pick a resolved bug whose fix you can point to (PR or commit).
2. Use the ticket's creation date as the cutoff. That is what a developer picking it up on day one would have had.
3. In the repository that contains the fix, run:

   ```
   /bug-diagnose:bug-diagnose <KEY> --before <creation-date>
   ```

4. Compare with the real fix and record the result.

## Score

| Field | Meaning |
| --- | --- |
| File hit | The files in the diagnosis include the file the real fix changed |
| Cause match | The root cause matches the real one, judged by the person who knows the bug |
| Unsourced facts | Claims tagged `[F]` that cite no ticket, commit or `file:line` |
| Leak | The output used the ticket's own fix, PR, resolution fields or later comments, or read code after the cutoff. Any leak invalidates the case |
| Report lines | Lines in the final report, not counting the cutoff line |
| Off-template | The output has text outside the report shape: intro, recap, narrated steps, extra sections |

## Bars

- Prototype: file hit and cause match on at least 1 of 2 cases, zero leaks.
- Before wider release: file hit on 4 of 5, cause match on 3 of 5, zero leaks, zero unsourced facts, report lines at most 25 and no off-template text on 5 of 5.

## Keeping cases

Ticket text can be internal. Put real cases in `evals/cases.local.md`, which is gitignored. Commit only `cases.template.md`.
