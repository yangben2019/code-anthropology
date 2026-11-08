# Code Anthropology

[![skills.sh](https://skills.sh/b/yangben2019/code-anthropology)](https://skills.sh/yangben2019/code-anthropology)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

**Read a codebase like a culture** — give it a personality, name its dynasty, and write its obituary before it is deleted.

Three Claude Code skills that turn structural facts about a repository into something people remember and act on. Every claim is backed by a number measured from the repo — not a vibe.

---

## Skills

| Skill | What it does |
|---|---|
| **project-personality** | Measures file sprawl, test density, commit rhythm, TODO debt, and junk-drawer directories, then assigns a personality (Perfectionist, Hoarder, Hermit, Tyrant, Farmer…) with evidence for each trait. |
| **dynasty-diagnosis** | Reads your architecture's *governance* — who decides, how far orders travel, whether the borders are open — and names it as a Chinese dynasty (Qin, Zhou, Tang, Song, Ming, Qing), with a Western empire equivalent. |
| **tech-debt-eulogy** | When a module is deleted or a service is sunset, writes a real obituary: born/died dates from git, cause of death, epitaph, and an inheritance list of what must be salvaged first. |

---

## Install

### Option 1 — skills CLI

```bash
npx skills add yangben2019/code-anthropology
```

Install a single skill only:

```bash
npx skills add yangben2019/code-anthropology --skill project-personality
```

### Option 2 — Claude Code plugin marketplace

```text
/plugin marketplace add yangben2019/code-anthropology
/plugin install code-anthropology@code-anthropology
```

CLI equivalent:

```bash
claude plugin marketplace add yangben2019/code-anthropology
claude plugin install code-anthropology@code-anthropology
```

### Option 3 — manual

```bash
git clone https://github.com/yangben2019/code-anthropology.git /tmp/ca
cp -r /tmp/ca/skills/* ~/.claude/skills/
```

---

## Usage

Just ask in plain language — the skills trigger on their own.

```text
What kind of project is this? Give me its personality.
```

```text
Which dynasty is this codebase?
```

```text
We're deleting the legacy billing module next week.
Write its obituary first and tell me what we should salvage.
```

Each skill runs read-only git and filesystem commands. Nothing is modified.

---

## Design notes

- **Evidence over vibes.** A personality type needs at least three measured signals. A dynasty needs measured governance signals. A eulogy needs dates from `git log`, not memory.
- **One recommendation, not ten.** A list of fixes gets ignored; one fix gets done.
- **No shaming.** "This repo hoards" is a trait; "this repo is bad" makes people stop reading.
- **Read-only by default.** These skills analyze; they do not refactor, delete, or push.

---

## License

MIT © 2026 yangben2019
