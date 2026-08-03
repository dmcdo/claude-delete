# claude-delete

List and delete local Claude Code session files (`~/.claude/projects/<project>/*.jsonl`).

This repo also includes [`claude-clone`](#claude-clone), a companion tool for cloning sessions.

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

# claude-clone

Clone local Claude Code session files, so you can branch off a conversation without touching the original.

A session transcript embeds its own id (`"sessionId":"..."`) on every line, including in any subagent transcripts under its side directory — a plain file copy would leave two sessions claiming the same id. `claude-clone` copies the session under a freshly generated id and rewrites those references throughout the copy (and only the copy), so the clone works as a fully independent session.

## Install

```
curl -fsSL https://raw.githubusercontent.com/dmcdo/claude-delete/refs/heads/master/claude-clone -o ~/.local/bin/claude-clone
chmod +x ~/.local/bin/claude-clone
```

Requires Python 3.10+ (stdlib only, no dependencies).

## Usage

```
claude-clone [--project PROJECT] [--all] {list,clone} ...
```

With no subcommand, runs in interactive mode: lists sessions and prompts for what to clone.

`--project` and `--all` work the same as in `claude-delete` — see [above](#usage) — and apply to every subcommand, before it.

### list

Same as `claude-delete list`.

### clone

```
claude-clone clone SESSION [SESSION ...] [--to PROJECT] [--dry-run] [--yes/-y]
```

Each `SESSION` can be a number (from `list`), a full session id, an id prefix, or `all` (every session currently in scope), same as `claude-delete delete`.

By default each session is cloned alongside itself, into the same project. Pass `--to PROJECT` to clone into a different project instead — given as a directory name under `~/.claude/projects`, a path to a project's source directory (its `~/.claude/projects` slug is computed for you), or a new slug to create:

```
claude-clone clone 1
claude-clone clone a1b2c3 --to otherapp
claude-clone --all clone all --to ~/Code/archive
```

Cloning more than one session prompts for confirmation unless you pass `--yes`/`-y`. `--dry-run` shows what would be cloned without cloning anything (and skips the prompt).

### Interactive mode

```
claude-clone
claude-clone --project myapp
```

Lists sessions, then prompts for a selection: a comma-separated list of numbers/id prefixes, `all`, or blank to quit. Clones land alongside their originals. Multiple selections ask for confirmation before cloning.

## Notes

- Cloning a session also clones its associated side directory (`<project>/<session-id>/`), if one exists, including subagent transcripts.
- `CLAUDE_CONFIG_DIR` overrides the default `~/.claude` config location.
