# bug-diagnose

Diagnose a Jira bug from evidence. Given a ticket key, the plugin reads the ticket, finds similar resolved bugs, finds the commits that fixed them, reads the code as it is today, and proposes a probable root cause, where to look, and how to validate.

## Requirements

- An Atlassian MCP with Jira access. Either the `atlassian` plugin from `claude-plugins-official` or an Atlassian connector. Authentication is per developer, so nothing is stored in this plugin.
- `git` in the repository you run it from. `gh` is optional and used as a fallback to find PRs.

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

Or just ask: "diagnose PROJ-123". With no key, it looks at the current branch name.

## What it does

1. Fetches the ticket and extracts distinctive error tokens.
2. Looks at the ticket's links, then searches Jira for up to 3 similar resolved bugs.
3. Hands those keys and the error tokens to the `bug-history-investigator` agent, which finds each fix in git and summarizes it, keeping large diffs out of the conversation. When Jira finds fewer than 2 similar bugs, the agent also searches git history by token.
4. Reads the suspect code now, and checks history for regressions.
5. Reports the problem, similar bugs, a diagnosis with confidence (each claim tagged `[F: source]` or `[H]`), and an action plan, in a fixed shape of at most 25 lines.

## Limits

- Fix lookup depends on the ticket key appearing in commit messages or PR titles. Repositories that do not do this will get weaker results, and the output says so.
- The skill only uses read operations on Jira and does not edit your repository unless you ask. This is an instruction to the model, not a permission boundary. If you need a hard guarantee, deny the Atlassian write tools in your own Claude Code permissions.
- Similar-bug search relies on JQL text matching, which is weak for code terms.
- The plugin does not run tests. It reads your repository docs to suggest how to validate, and says so when there are no tests.

## Testing the plugin

See [evals/README.md](evals/README.md).
