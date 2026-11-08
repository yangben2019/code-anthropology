---
name: tech-debt-eulogy
description: Write a proper obituary when a feature, module, dependency, or service is deleted or sunset — born and died dates from git history, cause of death, an epitaph, and an inheritance list of what must be salvaged before it disappears. Use when removing code, deprecating a feature, dropping a dependency, retiring a service, or when the user wants a written record before a deletion is irreversible.
---

# Tech Debt Eulogy 技术债葬礼

Something is about to be deleted. Write its obituary before it goes — so the decision is recorded, the useful parts are rescued, and nobody has to guess in two years why it disappeared.

## Language

Write the obituary in the user's language. Keep code identifiers, file paths and commit hashes as they are. A eulogy written in a language the team does not read is not a record.

## 中文说明

代码要删了，先写讣告再动手。

1. **从 git 取真实日期**：首次提交、最后提交、提交数、贡献者、增删行数
2. **判定死因**：被取代 / 无人使用 / 维护成本过高 / 上游死亡 / 安全 / 已合并 / 需求消失
3. **遗产清单**：值得移植的边界处理、测试、文档、配置——这一步最容易被跳过
4. **输出**：讣告、墓志铭、遗产表、葬礼清单

规则：日期一律取自 git，不凭记忆；庄重但不煽情；模块还活着就拒绝，改问是否需要弃用计划。

## Why bother writing this down

Deletions are the least documented events in a codebase. Additions get PRs, commits, and issues. Removals get a one-line commit message and then the knowledge is gone. Six months later someone asks "why don't we support X anymore" and the answer takes a week of archaeology.

The eulogy is a one-page answer to that question, written while the person who knows is still here.

## Workflow

### 1. Reconstruct the life

Pull real dates from git — do not estimate.

```bash
# when it was born
git log --reverse --format='%ad %h %s' --date=short -- <path> | head -1

# when it last changed
git log -1 --format='%ad %h %s' --date=short -- <path>

# how much happened in between
git log --oneline -- <path> | wc -l

# who was involved
git log --format='%an' -- <path> | sort | uniq -c | sort -rn | head -5

# how big it got
git log --numstat --format='' -- <path> | awk '{a+=$1; d+=$2} END {print a" added, "d" removed"}'
```

### 2. Determine cause of death

Name it precisely. "Deprecated" is not a cause.

- **Superseded** — replaced by X, which does the same thing better
- **Abandoned** — nobody used it in N months (say N, from usage evidence)
- **Too expensive** — maintenance cost exceeded value (say what the cost was)
- **No longer supported** — upstream died, we cannot carry it
- **Security** — it became a liability
- **Merged** — its function moved into Y
- **Circumstance** — the product, customer, or requirement went away

### 3. Salvage what matters

This is the part people skip and later regret. Before deletion, list anything worth carrying forward:

- Edge cases it handled that the replacement may not
- Tests that encode real-world behavior
- Documentation or comments explaining a non-obvious constraint
- Config values, environment quirks, or deployment knowledge
- Algorithms worth transplanting

If nothing is worth salvaging, say so explicitly — that is itself a useful finding.

### 4. Write it

```markdown
# Obituary: <module name>

**Born:** 2021-03-14 · **Died:** 2026-11-01 · **Lived:** 5 years, 7 months
**Commits:** 214 · **Contributors:** 6 (most: alice@…, 141)
**Net:** 8,420 lines added, 9,100 removed

## Cause of death
<one of the causes above, stated precisely>

## What it did
<two or three sentences, factual, for someone who never saw it>

## Epitaph
<one line — the thing worth remembering>

## Legacy
| Item | Where it goes |
|---|---|
| Retry-with-jitter logic | Transplanted into `core/net/retry.py` |
| The 2019 compat shim | Nothing — it was load-bearing for one customer, check with @bob |
| ... | ... |

## Burial checklist
- [ ] References removed (`grep -rn "<name>"`)
- [ ] Deprecation notice served for N release cycles
- [ ] Docs and changelog updated
- [ ] Data migration handled (if it owned state)
- [ ] Owner notified
```

## Rules

- **Dates come from git, not from memory.** If you cannot get the birth date, say "unknown" rather than guessing.
- **Dignity, not melodrama.** A little ceremony is the point; excessive pathos makes the document useless as a record. Every metaphor should hang on a real fact.
- **Refuse if it is still alive.** If the module is in active use, stop and ask whether the user actually wants a deprecation plan instead.
- **The legacy list is the whole value.** If you cannot name one thing worth salvaging after reading the code, read again before concluding nothing is there.
