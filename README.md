# consistent-code-review

A repeatable code review agent skill for Claude Code, Codex and Cursor that gives the same answer twice. Every finding needs a file, a line and the code itself, or it isn't reported. The checklist runs in a fixed order and findings are sorted the same way every time. A re-run is reconciled against the previous report, matched by a hash of the reviewed code. It doesn't promise bit-identical output from a model. It makes re-runs line up and shows you exactly what changed between them.

Install: `npx skills add arm-pz/consistent-code-review --skill consistent-code-review`

Ask it things like:

- "Review this PR diff."
- "Find bugs in OrderService.cs."
- "Last time you flagged four issues, now seven different ones. Same file."
- "Here's yesterday's review. Run it again on the same commit."

## What you get

| Part | What it does |
|---|---|
| Scope lock | Exact files or diff, recorded in the header, never widened |
| Input ID | First 12 hex characters of the SHA-256 of the scoped code, so two runs can be matched |
| Fixed checklist | Correctness, null handling, errors, resources, concurrency, security, data integrity, maintainability, style, in that order |
| Findings | CRITICAL / HIGH / MEDIUM / LOW / INCONCLUSIVE, each with `path:line`, the snippet, the reason and one fix direction |
| Baseline | A pasted earlier report or `.reviews/<Input ID>.md`. Earlier findings keep their IDs and anything new goes under "New since baseline", never hidden |

## Not for

Live security red-teaming of a running app (`pre-ship-security-red-team`), live performance scoring (`pre-ship-performance-audit`), or writing code and applying fixes (`production-code`). The two work as a pair: production-code sends its reviews here, and this skill offers to hand the findings you pick back to production-code to fix.

## Install

```
npx skills add arm-pz/consistent-code-review --skill consistent-code-review
```

Or copy `skills/consistent-code-review/` into your agent's skills directory (`.claude/skills/`, `.agents/skills/`, `.cursor/skills/` or `.qoder/skills/`). With Claude Code plugins, `.claude-plugin/plugin.json` makes the repo installable as a plugin.

## Structure

```
consistent-code-review/
├── README.md
├── LICENSE
├── .claude-plugin/
│   └── plugin.json
├── skills/
│   └── consistent-code-review/
│       └── SKILL.md          # scope lock, checklist, severity, baseline, output contract
└── tests/
    └── test-prompts.md
```

## Known limits

At temperature above 0, two first runs with no baseline can still differ. The baseline is what makes re-runs line up. The skill doesn't run code or tests unless you give it the results.
