# Engineering Journal — Rules of the Road

Read this before writing anything in this repo. If you ever feel the urge
to reorganize, re-template, or "finally do it properly" — read this file
again instead of acting on that urge.

**Purpose of this repo:** when you're stuck on a problem, you search a
concept, read what you learned about it across different projects, and you
decide faster. That is the only job this repo does. Every rule below exists
to protect that one job.

---

## 1. Structure — fixed

```
engineering-journal/
  README.md              ← flat index of concept files, one line each
  JOURNAL_RULES.md        ← this file
  concepts/
    <concept-name>.md
  projects/
    <project-name>.md     ← pointer file only
```

No domain folders (no `distributed-systems/`, no `caching/` as a
directory). No per-project subtree of notes. Two folders. That is the
whole structure, permanently.

If you catch yourself designing a taxonomy, stop. A taxonomy is
procrastination wearing the costume of productivity.

## 2. When to create a new concept file

Only when you would plausibly type that term into search while stuck on a
future problem. Not the moment you learn a fact. Not preemptively for
topics you haven't hit yet.

Good trigger: "I just fought with duplicate requests for 3 hours."
Bad trigger: "Idempotency is a category, so it deserves a file."

Naming: lowercase-kebab-case, named after the problem, not the tool.
`redis-rate-limiting.md`, not `redis.md`. `atomic-inventory-ops.md`, not
`lua.md`. You are indexing by the question you'll ask later, not by the
technology you happened to use.

## 3. Shape of a concept file — the only template allowed

```markdown
# <Concept>

## What I know so far
(3–6 lines. Current best understanding, rewritten in place as you learn
more. This is the part you read when stuck — keep it current, not a
history log.)

## <Project Name> — <Month Year>
Problem: ...
Approach: ...
Worked because: ...
Would NOT reuse when: ...

## Decision checklist
- If X → do A
- If Y → do B
```

Per-project sections: 5–10 lines, not essays. If something needs an essay,
it belongs in that project's own ADRs, not in this file.

No sections beyond this shape — no "failure modes," "maturity level,"
"tags block," "interview questions." If a concept seems to need more than
this template holds, that's a sign it deserves its own writeup elsewhere,
not a reason to grow the template.

## 4. When a new project touches an existing concept

Append a new dated section to the existing file. Never create
`redis-rate-limiting-v2.md` or a project-specific duplicate of a concept
that already has a file.

## 5. `projects/<name>.md` — pointer only

```markdown
# <Project Name>
One-line description. Link to the actual code repo.
Concepts touched: [concept-a](../concepts/concept-a.md),
[concept-b](../concepts/concept-b.md)
```

No real content here. It exists so you can browse project → concepts, not
only concept → projects. If it starts accumulating real content, that
content belongs in a concept file instead.

## 6. The only maintenance ritual

Whenever a concept file's "What I know so far" starts feeling stale or
wrong, rewrite it. Not on a schedule — just whenever it's true. That is
the entire maintenance burden. No metadata to update, no tags to
reconcile, no levels to bump.

## 7. Hard no's

- No domain-based folders.
- No maturity or skill levels per topic.
- No tag taxonomy or metadata blocks.
- No mandatory 10-section template per concept.
- No separate "failure playbook" document type.
- No reorganizing existing files around a new structural idea. A new idea
  gets added to this rules file for *future* entries only — never applied
  retroactively.

If a future idea (yours, or an AI's) proposes any of the above, reread
this section and decline.

## 8. Search over structure

Find things with `grep -ri "term" concepts/` or plain search, not by
browsing a folder tree. The two-folder layout exists only to give you one
obvious starting point (`README.md`) — not to be exhaustively browsable.
Don't optimize findability past "grep works."

## 9. Git workflow — one branch, commits carry project context

Everything is committed straight to `main`. No feature branches, no
per-project branches, no merging. There is nothing here that needs
isolating, and multiple projects append to the same concept file over
time — branches would only produce merge conflicts on notes that are
supposed to just keep growing.

Every commit message is prefixed with the project name:

```
<project-name>: <concept-file> — <what changed>
```

Examples:

```
flashsale: distributed-idempotency — added retry dedup section
flashsale: new concept file atomic-inventory-ops
otp-auth: jwt-authentication — added token refresh section
```

This makes git history the project index, with no extra structure to
maintain:

```
git log --oneline --grep="flashsale"                      # everything from one project
git log --oneline -- concepts/distributed-idempotency.md  # one concept's history across every project
```

---

**When unsure whether to restructure: don't.** A slightly imperfect flat
structure costs you a few extra seconds of searching. Restructuring costs
you days you don't get back, spent organizing instead of learning.
