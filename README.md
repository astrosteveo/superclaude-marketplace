# auto-dev

A [Claude Code](https://docs.anthropic.com/en/docs/claude-code) plugin that runs autonomous development loops using agent teams. Spawns brainstorm teams to generate ideas, then iterates with fresh dev agents to implement them.

## Install

```
claude plugin add astrosteveo/auto-dev
```

## Usage

```
/auto-dev                      # Build, project-scoped, 3 iterations
/auto-dev 5                    # Build, project-scoped, 5 iterations
/auto-dev auth                 # Build, feature-scoped ("auth"), 3 iterations
/auto-dev auth 5               # Build, feature-scoped ("auth"), 5 iterations
/auto-dev brainstorm           # Brainstorm only, project-scoped
/auto-dev brainstorm auth      # Brainstorm only, feature-scoped ("auth")
```

## How it works

### Brainstorm

Three agents with distinct perspectives debate and score ideas:

- **Architect** — owns structure, feasibility, and dependency mapping
- **Creative** — pushes for high-unlock-value features and lateral thinking
- **Critic** — deflates inflated scores, catches missing dependencies, identifies risks

They explore the codebase, propose scored items, debate for up to 3 rounds, then produce a prioritized `PROGRESS.md`.

### Build

Each iteration spawns a fresh dev agent (to avoid context poisoning) that:

1. Reads `PROGRESS.md` and picks the highest-priority unblocked item
2. Implements it with tested, committed code
3. Updates `PROGRESS.md` — marks completion, adjusts scores, unblocks dependencies

### Scoping

- **Project-scoped** (no feature arg): agents have full creative freedom to decide what's most valuable
- **Feature-scoped** (feature arg): agents stay focused on that feature, filtering `PROGRESS.md` without deleting unrelated items

### Scoring

Every item in `PROGRESS.md` is scored:

| Metric | Description |
|--------|-------------|
| **I** (Immediate value, 1-5) | Direct value right now |
| **U** (Unlock value, 1-5) | Future work this enables |
| **E** (Effort, 1-5) | Estimated effort (1=trivial, 5=massive) |
| **Priority** | `(I + U) / E` — higher is better |

## Configuration

Add a `## Settings` section to `PROGRESS.md` to control what happens when the task queue is empty:

```markdown
## Settings
on-empty: brainstorm
```

| Value | Behavior |
|-------|----------|
| `brainstorm` | Auto-run brainstorm to generate new items (project-scoped default) |
| `ask` | Ask the user what to do (feature-scoped default) |
| `stop` | End the loop |

## License

MIT
