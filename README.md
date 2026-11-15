# session-lessons

English | [简体中文](README.zh-CN.md)

A Claude Code skill that distills **lessons** (mistakes + rules that constrain future behavior) from a session into project docs, so future sessions stop repeating the same mistakes.

## The problem it solves

- Mistakes an agent made in one session get repeated in the next
- Lessons stay buried in conversation, never reaching a place where they actually take effect
- Post-mortem conclusions exist only as spoken words and are lost by the next session

## At most 1 interruption per run

| Mode | Interruptions | How |
|---|---|---|
| Standard (default) | **1** | say "沉淀教训" / "lessons" |
| Direct-write | **0** | say "沉淀教训 直接写" / "auto" |
| Standing authorization | **0, forever** | say once "以后沉淀教训直接写" — the authorization line lands in AGENTS.md, auto direct-write in this project afterwards |

The single interruption is one **merged checklist**: ledger entries, rule-body edits, and promotion proposals in one numbered list — reply "all" or pick numbers, everything lands, no second round. Every open decision (repo root, ledger language, pointer file, rule targets, dedup) is decided by the agent and shown as a defaulted line on the checklist — the user picks, never composes.

**Vendor-neutral**: the skill uses no vendor's built-in memory. Everything lands in the repo — the `LESSONS.md` ledger plus `AGENTS.md`/`CLAUDE.md` pointers and rules — so any agent that reads AGENTS.md (Claude Code, Codex, Cursor, Cline, …) reads the same lessons.

## How it works

```
Ground (repo root / ledger language / dedup index — all automatic)
    ↓
Inventory (≤7 candidates; each must pass the future-behavior test)
    ↓
Checklist (the one interruption) ←──── reply lands everything, no second round
    A ledger entries   B rule bodies   C promotions
    ↓
Write (LESSONS.md + AGENTS.md/CLAUDE.md pointer + rule bodies + promotions)
    ↓
Report (one table; direct-write adds a rollback list)
```

## Entry format

```markdown
### 2026-11-15 | No markdown syntax in published body copy
- Trigger: SH024's body went live with 22 backticks + 20 asterisks
- Root cause: the source file was markdown; backfill copied it verbatim
- Rule: body copy is always plain text
- Status: promoted (→ preflight.js hard block)
```

A recurrence appends `- Recurrence: YYYY-MM-DD` to the existing entry instead of creating a new one; at 3 occurrences, promotion to a hard rule is proposed.

## Design tenets

1. **Interruption budget** — one merged checklist in standard mode, zero in direct-write; open decisions are self-resolved
2. **Vendor-neutral** — no built-in memory of any vendor; everything lands in the repo, shared by every agent that reads AGENTS.md
3. **The future-behavior test** — a candidate counts only if you can name the behavior it constrains; feature lists, plans, and play-by-play fail it
4. **Single source of truth** — rule text lives where it takes effect (rules structures, main constraint file, script gates); the ledger holds one-line pointers only. Docs never copy numbers from code or config — copied numbers drift
5. **Promotion discipline** — a lesson seen 3+ times is proposed for promotion to a hard rule in the project's main constraint file; the ledger entry is marked "promoted" with a pointer
6. **Keep the ledger thin** — append-only; recurrences add a line, not a new entry
7. **Stay in bounds** — project files only; at most one pointer; never auto-commits

## Install

**Option 1: npx skills (recommended)**

```bash
npx skills add chenxuhl/session-lessons -g
```

**Option 2: git clone + copy**

```bash
git clone https://github.com/chenxuhl/session-lessons.git
mkdir -p ~/.claude/skills/session-lessons
cp session-lessons/SKILL.md ~/.claude/skills/session-lessons/SKILL.md
```

**Option 3: manual** — copy `SKILL.md` to `~/.claude/skills/session-lessons/SKILL.md`

## Usage

- Say "沉淀教训" / "lessons" / "retrospective" to trigger; best right after a release or a notable post-mortem
- Skip the picks: say "沉淀教训 直接写" — direct-write, ends with a precise rollback list
- Silence forever: say once "以后沉淀教训直接写" — the grant lands in AGENTS.md, and future runs in this project go direct
- The ledger's language follows the project's existing docs (SKILL.md itself is English, agent-facing; it does not affect the ledger's language)

## FAQ

**How do I remove an entry?** Append-only is a discipline, not a lock — delete by hand. Entries fully covered by hard rules are worth periodic cleanup.

**Won't direct-write go rogue?** No. It obeys the same bounds: append-only, project files only, rule bodies land where they take effect, never commits. Every run ends with a per-file rollback list.

**Do other vendors' agents work?** Fully — that is the point of being vendor-neutral: the skill uses no vendor's memory; everything lands in repo files (`LESSONS.md` + `AGENTS.md`/`CLAUDE.md`), and any agent reading AGENTS.md shares the same value.

## License

MIT
