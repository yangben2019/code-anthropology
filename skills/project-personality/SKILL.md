---
name: project-personality
description: Give a code repository a memorable personality type backed by measured evidence — file sprawl, test density, dependency count, commit rhythm, TODO debt, directory depth, junk-drawer folders. Use when the user asks what kind of project this is, wants a fun or diagnostic read on a codebase, mentions project personality, 项目人格, 代码库体检, repo health check with a twist, or wants to understand a codebase's working style before joining, inheriting, or refactoring it. Prefer this over a plain health score when the user wants the finding to stick in memory.
---

# Project Personality

Read a repository's habits and give it a personality type — but every trait must trace to a number you measured in this session. The point is not to be cute. It is to make structural facts memorable enough that someone actually acts on them.

## Why evidence first

People ignore dashboards and remember stories. A label like "this repo is a hoarder" only helps if the reader can verify it in thirty seconds. So the order is always: **measure → name → point at the proof**. A personality claim with no number behind it is a horoscope, and users can smell the difference.

## Workflow

### 1. Measure

Run these in the project root. Adapt to the platform — on Windows without a POSIX shell use `git ls-files` and `Get-ChildItem -Recurse`. What matters is that you actually ran them, not which syntax you used.

```bash
# size and shape
git ls-files | wc -l
git ls-files | sed 's/.*\.//' | sort | uniq -c | sort -rn | head -10

# biggest files
git ls-files | grep -E '\.(py|ts|js|java|go|rs|cpp|c|kt)$' | xargs wc -l 2>/dev/null | sort -rn | head -5

# tests vs sources
git ls-files | grep -cE 'test|spec'
git ls-files | grep -E '\.(py|ts|js|java|go|rs|cpp|c|kt)$' | grep -vcE 'test|spec'

# debt markers
git grep -c -E 'TODO|FIXME|HACK|XXX' | wc -l

# junk drawers
git ls-files | grep -E '(^|/)(utils|common|misc|helpers|shared|lib)/' | wc -l

# history
git log --oneline | wc -l
git shortlog -sn --all | head -5
git log -1 --format='%ar'
git log --format='%ad' --date=format:'%Y-%m' | sort | uniq -c | tail -6
```

Also useful when present: `package.json` / `requirements.txt` / `pom.xml` / `Cargo.toml` dependency counts, and whether CI config exists (`.github/workflows`, `.gitlab-ci.yml`).

### 2. Classify

Match measurements to the catalog. Repos usually have one dominant type and one secondary type. If two tie, say both and explain the split — a mixed personality is a real finding, not a failure to decide.

| Type | How you see it | What it costs |
|---|---|---|
| **The Perfectionist** 控制狂 | Test files ≥ 60% of source files; CI config; linter; few TODOs | Ships slowly; can over-engineer small features |
| **The Hoarder** 囤积狂 | 2+ junk-drawer dirs; historically few deletions; high file count per feature | Nobody dares delete anything; onboarding takes weeks |
| **The Hermit** 隐士 | README missing or under 10 lines; zero tests; low comment density; still works | Only one person understands it |
| **The Loner** 独行侠 | Near-zero third-party dependencies; reimplements things the stdlib already has | Reinvents wheels; slow to adopt fixes |
| **The Performer** 表演者 | Long README with badges and screenshots; tests ≈ 0 | Looks healthy from outside; breaks on first contribution |
| **The Tyrant** 暴君 | One file > 1500 lines or > 25% of total LOC | Every change touches the same file; merge conflicts forever |
| **The Renovator** 拆迁户 | High structural churn: frequent renames, moves, refactors; still 0.x after years | Nothing is ever finished; new contributors lose the map |
| **The Farmer** 老农 | Steady small commits over years; stable directories; low TODO count | Slow to change direction; may be missing the rewrite it needs |
| **The Nomad** 游牧者 | Three or more different HTTP clients / state libraries / ORMs across history | No single mental model; every layer is from a different era |

### 3. Report

Use this structure exactly — it keeps the claim checkable:

```markdown
# Project Personality: <type>（<中文名>）

**One-liner:** <one sentence a colleague would repeat to someone else>
**Confidence:** high / medium / low — because <what is uncertain>

## Evidence

| Signal | Measured | What it means |
|---|---|---|
| Test files vs source files | 12 / 340 | Almost no safety net for refactoring |
| Junk-drawer files | 89 in `utils/`, `common/` | ... |
| Largest file | `core/engine.py` — 3,120 lines | ... |
| Commits (last 6 months) | 7 | ... |

## Strengths
<what this personality is actually good at — every type has one>

## Risks
<what breaks first if nothing changes>

## One thing to do this week
<one concrete action, with the file or directory named>
```

## Rules

- **Three measured signals minimum** per type claim. If you have fewer, lower the confidence and say what you could not measure.
- **Never shame.** Describe working style, not competence. "This repo hoards" is a trait; "this repo is bad" is an insult and users stop listening.
- **Cite files, not vibes.** `core/engine.py:1-3120` beats "the code is monolithic".
- **One recommendation.** A list of ten fixes gets ignored; one fix gets done.
- If the repo is too small (under ~20 files) for the signals to mean anything, say so and stop rather than guessing.
