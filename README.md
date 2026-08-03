# claude-delete

List and delete local Claude Code session files (`~/.claude/projects/<project>/*.jsonl`).

## Install

```
curl -fsSL https://raw.githubusercontent.com/dmcdo/claude-delete/refs/heads/master/claude-delete -o ~/.local/bin/claude-delete
chmod +x ~/.local/bin/claude-delete
```

Requires Python 3.10+ (stdlib only, no dependencies).

## Usage

```
claude-delete [--project PROJECT] [--all] {list,delete} ...
```

With no subcommand, runs in interactive mode: lists sessions and prompts for what to delete.

`--project` and `--all` apply to every subcommand and must come *before* the subcommand:

- `--project PROJECT` — target a specific project, given either as a directory name under `~/.claude/projects` or a path to the project's source directory. If omitted, the tool tries to match the current working directory to a project; if that fails, it prompts you to pick one from a list.
- `--all` — operate across every project instead of just one.

### list

```
claude-delete list
claude-delete --project myapp list
claude-delete --all list
```

Shows each session's id, last modified time, size, message count, and a preview of the first user message.

### delete

```
claude-delete delete SESSION [SESSION ...] [--dry-run] [--yes/-y]
```

Each `SESSION` can be a number (from `list`), a full session id, or an id prefix. You can pass several at once:

```
claude-delete delete 1 2 3
claude-delete delete a1b2c3 f9e8d7
```

Use `all` to delete every session currently in scope (the current project, or every project with `--all`):

```
claude-delete delete all
claude-delete --all delete all
```

`all` can't be combined with other selectors.

Deleting more than one session prompts for confirmation unless you pass `--yes`/`-y`. `--dry-run` shows what would be removed without deleting anything (and skips the prompt).

### Interactive mode

```
claude-delete
claude-delete --project myapp
```

Lists sessions, then prompts for a selection: a comma-separated list of numbers/id prefixes, `all`, or blank to quit. Multiple selections ask for confirmation before deleting.

## Notes

- Deleting a session also removes its associated side directory (`<project>/<session-id>/`), if one exists.
- `CLAUDE_CONFIG_DIR` overrides the default `~/.claude` config location.
