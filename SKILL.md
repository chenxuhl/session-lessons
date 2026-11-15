---
name: session-lessons
description: "Distill a session's lessons - mistakes plus the rules that constrain future behavior - into the project's docs: a LESSONS.md ledger plus rule bodies in the files where they take effect, so future sessions stop repeating them. Use when the user says 「沉淀教训」「复盘教训」「lessons」, or right after a release, incident, or major change lands (propose it then)."
---

# Session Lessons

You are distilling this session's **lessons** into the project's docs so future sessions don't repeat them. Only **lessons** enter the **ledger**: what was built and what shipped stays in the project's own records. Everything lands in the repo — ledger, pointers, rule bodies — so any agent on any vendor reads the same lessons.

The run costs the user at most **one interruption**: a single merged checklist, one reply, everything lands. **Direct-write** costs zero. Every open decision (repo root, ledger language, pointer file, rule targets, dedup) you decide yourself and present as a defaulted line on the checklist — the user picks, never composes. If you catch yourself about to ask an open question ("where should this rule live?"), **stop: that is the exact failure this rule prevents.** Decide it, and put it on the checklist.

## Modes

- **Standard** (default): the checklist is the run's only interruption. One reply — "all", numbers, "all except 5", or edits to the list — approves everything at once. There is no second round.
- **Direct-write** (zero interruptions): fires when the invocation contains 「直接写」/「自动沉淀」/"direct" / "auto" / 「不用确认」/「别问我」. Skip the checklist, execute everything, end with a **rollback list**.
- **Standing authorization**: when the user once says 「以后沉淀教训直接写」 or similar, write the grant as a line in the project's agent file (AGENTS.md preferred, else CLAUDE.md; format in Reference), switch to direct-write for this run, and honor it in this project's future runs — every agent reading that file sees the grant.

All modes share the same bounds: the ledger is **append-only**; only project files are touched; rule bodies land where they take effect; commits stay with the user.

## Steps

### 1. Ground

Pin the facts every later step consumes:

1. Repo root: `git rev-parse --show-toplevel`; no git → current directory.
2. Ledger language: an existing `LESSONS.md` wins; else the majority language of README / AGENTS.md / CLAUDE.md; else the session's language. New rule bodies follow the project docs.
3. Read the existing `LESSONS.md`, if any. It is your **dedup** index and **recurrence** counter. Leave every existing entry byte-for-byte intact; new entries follow the entry format in Reference.
4. Session context compacted? Reconstruct what changed from `git status`, `git diff`, and this session's commits.

Done when root, language, and prior-lesson counts are pinned.

### 2. Inventory

List candidate lessons — one line each, capped at 7, ranked by future impact. A candidate is a lesson only if it passes the **future-behavior test**: you can name the behavior it forbids or mandates next session. Feature lists, plans, and play-by-play fail the test.

Do not pad. A thin session yields zero or one lessons; inventing a candidate to fill the list is the failure this test prevents.

Done when every listed candidate passes the test. If none does, say "no lessons worth keeping" and stop — zero files created, zero interruptions spent.

### 3. The checklist (the one interruption)

Present one merged, continuously numbered checklist. A multi-select question tool (e.g. Claude Code's `AskUserQuestion` with `multiSelect`) is welcome when the items fit; otherwise a numbered list.

- **A — Ledger entries**: new lessons. A candidate that substantially repeats an existing entry is not a new entry: mark it a **recurrence** (date) against the old one.
- **B — Rule bodies**: where each lesson's rule actually lands — file, section, the sentence to add or change. Collaboration habits (true of any project, about how the agent works) land in the agent file, where every vendor's agent reads them; project-content lessons land in the topical file (rules structures, docs, the file where the behavior is enforced). No fitting home → say so; the entry stays **unpromoted**.
- **C — Promotions**: an entry whose recurrence count reaches 3 with this session's hit → propose promoting it to a **hard rule** in AGENTS.md/CLAUDE.md or the project's existing rules structure, naming the target file.

On a proactive trigger (post-release, post-incident), skip the "shall we do a retrospective?" preamble: the checklist itself is the informed consent, and "no" ends the run with nothing written.

```
2 lessons this session:

A Ledger entries
1. Release copy must not carry markdown syntax
2. Recurrence (2026-11-15) — copy must be plain text: hits the 2026-10-02 entry, count now 3

B Rule bodies
3. Lesson 1 → docs/release.md "Copy rules" section: add "copy is plain text; strip markdown before paste-back"
4. Lesson 2: no fitting home yet — entry stays unpromoted

C Promotions
5. Lesson 2 hits 3 → AGENTS.md hard rule: release copy is plain text
```

In direct-write mode, skip this step entirely.

### 4. Write

Execute the selected lines, in order:

1. **Ledger**: `LESSONS.md` absent → create from the template in Reference. Append entries in the entry format. A dedup hit appends one `- Recurrence: YYYY-MM-DD` line to the existing entry — existing lines untouched. An entry selected without its B line stays **unpromoted**; with its B line selected it reads **promoted (→ file)**.
2. **Pointer**: one line pointing at `LESSONS.md`, in AGENTS.md (preferred) or CLAUDE.md. A pointer already exists → skip. Neither file exists → skip; never create a file just to host a pointer.
3. **Rule bodies**: exactly as the selected B lines specify.
4. **Promotions**: the selected C lines into their target files; the entry flips to **promoted (→ file)**.

Done when every selected line has landed in its named file.

### 5. Report

One table, then stop — no follow-up questions:

| File | Action | What |
|---|---|---|
| LESSONS.md | created | 1 entry |
| docs/release.md | edited | "Copy rules" section +1 line |

Direct-write mode appends the **rollback list**: per file, what changed and how to revert (git commands when git is available; warn that the worktree may carry other uncommitted changes).

Done when every file touched appears in the table — a touched file missing from the table is a miss.

## Reference

### Entry format (exact)

Chinese ledger:

```markdown
### YYYY-MM-DD｜一句话教训
- 触发：当时发生了什么
- 根因：为什么会犯
- 规矩：以后怎么做（一句话）
- 状态：提醒中 / 已升级（→ 规矩所在文件）
```

English ledger:

```markdown
### YYYY-MM-DD｜<one-line lesson>
- Trigger: what happened
- Root cause: why it happened
- Rule: what to do from now on (one line)
- Status: unpromoted / promoted (→ file)
```

A recurrence appends one line (existing lines untouched): `- 重复：YYYY-MM-DD` / `- Recurrence: YYYY-MM-DD`. Recurrence count = the entry itself + its recurrence lines; at 3, propose promotion (step 3, section C). Dates carry the lesson's date, not the session's.

### New-ledger template

Chinese project:

```markdown
# Lessons（会话教训台账）

> 只追加不改历史；同一教训累计 3 次升级为硬规则。规矩正文在其生效文件，此处只放指针。
```

English project:

```markdown
# Lessons

> Append-only. A lesson seen 3+ times gets promoted to a hard rule. Authoritative rules live where they take effect; this ledger only holds pointers.
```

### Pointer line

Chinese: `累计教训台账见 LESSONS.md——复盘追加，重复 3 次的教训升级为硬规则`

English: `See LESSONS.md for the lessons ledger — append on retro; a lesson seen 3+ times gets promoted to a hard rule.`

### Standing-authorization line

Written once, in the agent file, when the user grants it. Makes direct-write visible to every agent that reads this file.

Chinese: `session-lessons 已授权直写（自 YYYY-MM-DD）：沉淀教训无需确认，直接落盘`

English: `session-lessons: direct-write authorized (since YYYY-MM-DD) — run lessons without asking.`

### Single source of truth

Rule bodies live in the files where they take effect (rules structures, gates in scripts, the main constraint file); the ledger holds one-line pointers only. Docs never copy numbers out of code or config — copied numbers drift.
