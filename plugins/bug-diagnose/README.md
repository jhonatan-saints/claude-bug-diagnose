# bug-diagnose

Triage a Jira bug from evidence. Given a ticket key, the plugin reads the ticket and its logs, finds similar tickets and the commits that fixed them, and classifies the bug as a duplicate, fixed in another version, a configuration issue or a new bug. It suggests the team and the next action, and for a new bug finds the code at fault and can open a draft PR.

## Requirements

- An Atlassian MCP with Jira access. Either the `atlassian` plugin from `claude-plugins-official` or an Atlassian connector. Authentication is per developer, so nothing is stored in this plugin.
- `git` in the repository you run it from. `gh` is optional: it is a fallback to find PRs, and it is needed to open a draft PR.

## Install

Add the marketplace, then install the plugin. Run the two commands one at a time.

```
/plugin marketplace add jhonatan-saints/claude-bug-diagnose
```

```
/plugin install bug-diagnose@bug-diagnose
```

## Use

Run it from the repository that contains the bug.

```
/bug-diagnose:bug-diagnose PROJ-123
```

Or just ask: "triage PROJ-123", "is PROJ-123 a duplicate?". With no key, it looks at the current branch name.

## What it does

1. Fetches the ticket, reads pasted logs and `.log`/`.txt` attachments, and extracts distinctive error tokens.
2. Looks at the ticket's links, then searches Jira for open tickets (duplicates) and resolved ones, keeping configuration and works-as-designed closures as evidence.
3. Hands the fixed tickets, error tokens and affected versions to the `bug-history-investigator` agent, which finds each fix in git, its authors and the first releases that contain it.
4. Classifies the bug: duplicate, fixed in another version, configuration, or new bug (and regression), with a confidence level.
5. Suggests a team from who handled the similar tickets and their fixes.
6. For a new bug or a configuration issue, reads the suspect code or setting.
7. Reports all of this in at most 25 lines, each claim tagged `[F: source]` or `[H]`, with up to 3 similar bugs and one next action.
8. For a new bug, offers to apply the change and open a draft PR. It only does this after you say yes.

## Limits

- Fix lookup and the team suggestion depend on ticket keys in commit messages or PR titles. Repositories that do not do this will get weaker results, and the output says so.
- "Fixed in another version" depends on release tags in git. Without tags, it says so instead of guessing.
- The skill only uses read operations on Jira, and it changes your repository only when you accept the draft PR offer. This is an instruction to the model, not a permission boundary. If you need a hard guarantee, deny the Atlassian write tools in your own Claude Code permissions.
- Similar-ticket search relies on JQL text matching, which is weak for code terms.
- The plugin does not run tests unless you accept the draft PR offer. It reads your repository docs to suggest how to validate, and says so when there are no tests.

## Testing the plugin

See [evals/README.md](evals/README.md).
