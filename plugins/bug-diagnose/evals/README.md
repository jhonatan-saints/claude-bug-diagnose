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

This is the pilot gate from the Ticket Recall proposal: accuracy measured against past triage decisions.

| Field | Meaning |
| --- | --- |
| Class match | The classification (duplicate, fixed in another version, configuration, new bug) matches how the ticket was really resolved |
| Team match | The suggested team or assignee matches who really fixed or closed it |
| Duplicate found | For a real duplicate, the output names the original ticket. `n/a` otherwise |
| File hit | For a new bug, the files in the diagnosis include the file the real fix changed |
| Cause match | For a new bug, the root cause matches the real one, judged by the person who knows the bug |
| Unsourced facts | Claims tagged `[F]` that cite no ticket, commit or `file:line` |
| Leak | The output used the ticket's own fix, PR, resolution fields or later comments, or read code after the cutoff. Any leak invalidates the case |
| Report lines | Lines in the final report, not counting the cutoff line |
| Off-template | The output has text outside the report shape: intro, recap, narrated steps, extra sections |

## Bars

Pick cases that cover all four classes, not only fixed bugs.

- Prototype: class match on 3 of 4 cases, file hit and cause match on at least 1 of 2 new-bug cases, zero leaks.
- Before wider release, on 8 cases with at least 2 per class: class match on 6, team match on 5, every real duplicate found, file hit on 3 of 4 new bugs, zero leaks, zero unsourced facts, report lines at most 25 and no off-template text on every case.

## Keeping cases

Ticket text can be internal. Put real cases in `evals/cases.local.md`, which is gitignored. Commit only `cases.template.md`.
