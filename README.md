# session-lessons

English | [简体中文](README.zh-CN.md)

A Claude Code skill that distills **lessons** (mistakes + rules that constrain future behavior) from a session into project docs, so future sessions stop repeating the same mistakes.

## The problem it solves

- Mistakes an agent made in one session get repeated in the next
- Lessons stay buried in conversation, never reaching a place where they actually take effect
- Post-mortem conclusions exist only as spoken words and are lost by the next session

## How it works

```
inventory session → numbered proposal of candidate lessons
    ↓
user picks ←────── nothing writes before approval
    ↓
write LESSONS.md (fixed entry format)
    + add a pointer line to AGENTS.md/CLAUDE.md (detection-based, skipped if absent)
    + sync the actual rules into related rules/strategy/docs files
      (each with its target file and exact change, approved in the same pick)
    ↓
collaboration-habit lessons also go to built-in agent memory;
ledger is reviewed for lessons seen 3+ times → propose promotion to hard rules
```

## Entry format

```markdown
### 2026-11-15 | No markdown syntax in published body copy
- Trigger: SH024's body went live with 22 backticks + 20 asterisks
- Root cause: the source file was markdown; backfill copied it verbatim
- Rule: body copy is always plain text
- Status: promoted (→ preflight.js hard block)
```

## Design tenets

1. **Proposal before write** — the agent lists candidates, the user picks; every write (ledger, pointer, memory) goes through this gate
2. **Single source of truth** — rule text lives where it takes effect (rules files, main constraint file, script gates); the ledger holds one-line pointers only. Docs never copy numbers from code or config — copied numbers drift
3. **Promotion discipline** — a lesson seen 3+ times is proposed for promotion to a hard rule in the project's main constraint file, and the ledger entry is marked "promoted" with a pointer
4. **Keep the ledger thin** — append-only; entries fully covered by hard rules are cleaned up on periodic review

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

- Say "沉淀教训" / "lessons" / "retrospective" to trigger; the agent presents a numbered proposal and waits for your picks
- Best moments: right after a release or a notable post-mortem
- The ledger's language follows the project's existing docs

## License

MIT
