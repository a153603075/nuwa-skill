# Using Nuwa Skill with Codex

This repository was originally authored as a Claude Code skill. It now also includes a Codex-native Agent Skill wrapper at:

```text
.agents/skills/nuwa-skill/SKILL.md
```

The original files are preserved. The Codex wrapper migrates the core Nuwa workflow to Codex conventions, especially the `.agents/skills/` install location.

## What this skill does

Nuwa helps Codex turn a person, private corpus, or topic into a reusable perspective skill. It guides Codex through:

1. Clarifying whether you already know the person/topic or need recommendations.
2. Collecting evidence from primary and secondary sources.
3. Extracting mental models, decision heuristics, expression DNA, values, anti-patterns, tensions, and honest limits.
4. Creating a new Codex skill under `.agents/skills/<name>/`.
5. Validating that the generated skill is useful, honest, and not just quote imitation.

## Use it inside this repository

If you open Codex in this repository, Codex can discover the repo-scoped skill from:

```text
.agents/skills/nuwa-skill/
```

Then ask Codex something like:

```text
Use $nuwa-skill to distill Paul Graham into a Codex perspective skill.
```

or in Chinese:

```text
使用 $nuwa-skill，帮我蒸馏一个张小龙的产品视角 Skill。
```

You can also start from a vague need:

```text
使用 $nuwa-skill。我想提升商业决策质量，但不知道该蒸馏谁，先帮我推荐候选。
```

## Install it for all Codex projects

To use Nuwa from any project, copy the skill directory to your user-level Codex skills directory:

```bash
mkdir -p ~/.agents/skills
cp -R .agents/skills/nuwa-skill ~/.agents/skills/nuwa-skill
```

Restart Codex after copying. Then invoke it from any workspace:

```text
$nuwa-skill create a Feynman-style learning advisor skill for Codex.
```

## Generate a new perspective skill

A typical output should be written under `.agents/skills/`, for example:

```text
.agents/skills/paul-graham-perspective/
├── SKILL.md
├── references/
│   ├── research/
│   │   ├── 01-writings.md
│   │   ├── 02-conversations.md
│   │   ├── 03-expression-dna.md
│   │   ├── 04-external-views.md
│   │   ├── 05-decisions.md
│   │   └── 06-timeline.md
│   └── sources/
└── scripts/        # optional
```

Ask for local primary materials if you have them. Good inputs include books, PDFs, transcripts, subtitles, long interviews, blog exports, social posts, decision memos, or private notes.

## Important Codex differences from the original Claude workflow

- Use `.agents/skills/` instead of `.claude/skills/`.
- Codex skills require a `SKILL.md` with YAML frontmatter containing `name` and `description`.
- Keep generated skills self-contained so they can be copied into another `.agents/skills/` directory.
- Put long research notes in `references/research/` instead of bloating the generated `SKILL.md`.

## Recommended first test

Before creating a brand-new skill, try a small request:

```text
Use $nuwa-skill to recommend 3 candidate thinking frameworks for improving product judgment. Do not create files yet; just diagnose and recommend.
```

If the recommendations look right, continue with:

```text
Proceed with the best candidate and create the Codex skill under .agents/skills/.
```
