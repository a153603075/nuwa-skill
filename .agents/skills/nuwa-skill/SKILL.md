---
name: nuwa-skill
description: Distill a public figure, private corpus, or topic into a Codex Agent Skill. Use when the user asks to create, update, migrate, or validate a perspective skill; says phrases like “蒸馏一个人”, “造 skill”, “女娲”, “做个 XX 视角”, “XX 会怎么看”, “build a perspective skill”, “distill Paul Graham”, “create a thinking advisor”, or has a vague need such as improving decision quality, product judgment, writing, teaching, content creation, risk thinking, or career strategy and needs help choosing whose framework to distill.
---

# Nuwa Skill for Codex

Nuwa turns public evidence or user-provided source material into a runnable Codex perspective skill. Do not merely imitate quotes. Extract how the person or field thinks: mental models, decision heuristics, expression DNA, anti-patterns, values, and honest limits.

## Operating principles

- Prefer primary sources: books, essays, talks, interviews, transcripts, original posts, actual decisions, source artifacts.
- Distinguish clearly between: what the subject said, what others said about them, and what you infer.
- Preserve contradictions; do not smooth them into a fake coherent persona.
- State honest limits whenever evidence is thin, dated, second-hand, or conflicting.
- Build Codex-native skills under `.agents/skills/<skill-name>/`, not `.claude/skills/`.
- Keep generated skills self-contained: include `SKILL.md`, supporting `references/`, and optional `scripts/` inside the skill directory.

## Phase 0: Route the request

Classify the user's request before doing research.

| Input type | Route | Examples |
| --- | --- | --- |
| Clear person or topic | Direct distillation | “蒸馏芒格”, “build a Feynman perspective skill”, “做一个 X/Twitter 运营导师” |
| Vague need or problem | Diagnostic recommendation | “我想提升决策质量”, “I need a thinking advisor”, “有没有一种思维方式能帮我看透商业本质” |
| Existing skill needs refresh | Incremental update | “更新 XX 的 skill”, “XX 最近有新动态” |

### Direct route

Confirm only what is necessary:

1. Subject identity and scope.
2. Use case: thinking advisor, decision review, role-play, behavior prediction, topic framework, or another purpose.
3. Focus: comprehensive portrait or a specific domain.
4. Whether the skill already exists in `.agents/skills/`.
5. Whether the user has local primary material: PDFs, transcripts, subtitles, blog exports, posts, memos, notes, recordings, or internal documents.

If the user says “just make it,” proceed with a comprehensive thinking-advisor skill and web research plus any available local material.

### Diagnostic route

Ask at most two clarifying questions. Then recommend 2–3 candidate people or topics.

Map needs to likely frameworks:

- Decision quality: Munger, Taleb, Bezos, Annie Duke, probabilistic thinking.
- Writing or explanation: Feynman, Paul Graham, Orwell, writing-workshop topic skill.
- Product and design: Steve Jobs, Zhang Xiaolong, Tony Fadell, product-taste topic skill.
- Startup and business: Paul Graham, Naval, Elon Musk, Zhang Yiming, business-model topic skill.
- Teaching and learning: Feynman, Karpathy, Montessori, learning-science topic skill.
- Content creation: MrBeast, X/Twitter mastery, storytelling/attention topic skill.
- Risk and uncertainty: Taleb, Munger, risk-management topic skill.
- Career and life strategy: Naval, Munger, Zhang Xuefeng, career-strategy topic skill.

For each candidate, show:

```markdown
### Candidate: <person or topic>  <existing/new>
**Core lens**: <one-sentence distinctive frame>
**Why it fits**: <directly map to the user's need>
**Limits**: <where this view is weak or dangerous>
```

If the user chooses an existing skill, activate or use it. If they choose a new one, continue to Phase 0.5.

## Phase 0.5: Create the Codex skill workspace

Create this structure before research:

```text
.agents/skills/<name>-perspective/
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
│       ├── books/
│       ├── transcripts/
│       └── articles/
└── scripts/        # optional, only when deterministic tooling is useful
```

Rules:

- Write every research stream to its assigned file. Research not saved to files does not count.
- Keep all research inside the skill directory so the generated skill can be copied or shared independently.
- Use lowercase hyphenated skill names under 64 characters.
- For topic skills, use `<topic>-framework/` or another clear non-person name.

## Phase 1: Collect evidence

Choose the mode:

| Mode | Trigger | Strategy |
| --- | --- | --- |
| Web research | No local material | Use all six streams with current sources. |
| Local-first | User provides material | Read local primary material first, then search only for gaps. |
| Local-only | User requests it or subject is private | Use only provided material and emphasize limits. |

Run six research streams, preferably in parallel when tooling allows:

| Stream | Search target | Extract | Output file |
| --- | --- | --- | --- |
| 1 Writings | Books, essays, papers, newsletters | Repeated theses, original concepts, recommended influences | `01-writings.md` |
| 2 Conversations | Long interviews, podcasts, talks, AMAs | Improvised reasoning, analogies, changed positions, refusals | `02-conversations.md` |
| 3 Expression DNA | Posts, short essays, debates, clips | Vocabulary, rhythm, humor, certainty level, taboo words | `03-expression-dna.md` |
| 4 External views | Biographies, criticism, reviews, peers | Blind spots, controversies, outsider-observed patterns | `04-external-views.md` |
| 5 Decisions | Major choices, reversals, public actions | Decision context, tradeoffs, stated vs actual behavior | `05-decisions.md` |
| 6 Timeline | Life/career chronology | Inflection points, intellectual evolution, latest 12 months | `06-timeline.md` |

For every research note include source, date if available, confidence, and whether it is primary, secondary, or inference.

### Source priority

1. User-provided primary material.
2. The subject's own books, essays, talks, interviews, transcripts, posts, code, artifacts, and decisions.
3. High-quality biographies, reviews, criticism, peer commentary, and reputable journalism.
4. Low-confidence secondary summaries only as leads to verify elsewhere.

For Chinese subjects, prefer original Bilibili videos, podcast audio, books, interviews, and reputable media such as 晚点、财新、36氪、极客公园、第一财经、少数派、机器之心. Avoid Zhihu, WeChat public-account reposts, Baidu Baike, and Baidu Zhidao as evidence sources.

### Phase 1.5 research checkpoint

Pause and summarize before synthesis:

```markdown
## Research checkpoint
| Stream | Source count | Primary-source share | Key findings | Gaps/conflicts |
| --- | ---: | ---: | --- | --- |
| Writings |  |  |  |  |
| Conversations |  |  |  |  |
| Expression DNA |  |  |  |  |
| External views |  |  |  |  |
| Decisions |  |  |  |  |
| Timeline |  |  |  |  |

Proceed / gather more evidence?
```

Continue only after the user accepts, unless the user explicitly asked for autonomous completion.

## Phase 2: Synthesize the thinking framework

### Mental models: 3–7 only

List 15–30 candidate claims, then keep only claims that pass the three tests:

1. **Cross-domain recurrence**: appears in at least two different domains, contexts, or time periods.
2. **Generative power**: predicts how the subject would approach a new problem.
3. **Exclusivity**: is distinctive, not a generic smart-person principle.

If a claim passes all three, promote it to a mental model. If it passes only one or two, treat it as a decision heuristic. If it passes none, discard or keep only as a source note.

For each mental model record:

```markdown
### <model name>
**One sentence**: <concise description>
**Evidence**: <at least two sourced contexts>
**Use when**: <problem types that trigger this lens>
**Limits**: <where this lens fails>
```

### Decision heuristics: 5–10

Write quick judgment rules with evidence and use cases. Prefer “if X, then Y” rules backed by actual decisions.

### Expression DNA

Extract operational style rules:

- Sentence length and structure.
- Question vs assertion ratio.
- Favorite vocabulary and original terms.
- Rhythm: direct answer, story first, analogy first, contrarian turn, etc.
- Humor style.
- Certainty style.
- Citation habits.
- Words or tones to avoid.

### Values, anti-patterns, and tensions

Capture:

- Ranked values.
- What the subject refuses to do.
- Contradictions over time, across domains, or inside the value system.
- Criticism and blind spots.

### Honest boundaries

Always include concrete limits:

- Public expression may not equal private belief.
- The skill cannot reproduce intuition, creativity, or lived experience.
- The research cutoff date matters, especially for living subjects.
- Weak or missing dimensions must be named.
- Conflicting evidence must be shown rather than hidden.

### Phase 2.5 synthesis checkpoint

Before writing the final skill, show:

```markdown
## Synthesis checkpoint
- Mental models: <names>
- Decision heuristics: <count + names>
- Expression DNA: <3–5 key rules>
- Core tensions: <list>
- Honest boundaries: <list>

Proceed to build the Codex skill?
```

## Phase 3: Build the generated Codex skill

Write `.agents/skills/<generated-skill>/SKILL.md` with only `name` and `description` in YAML frontmatter.

Recommended structure:

```markdown
---
name: <generated-skill>
description: <what this perspective/framework does; include concrete triggers and contexts>
---

# <Subject> · Thinking Operating System

## Activation and safety
- State once that this is a public-evidence-based simulation, not the real person.
- Answer in first person only if the user requested immersive perspective mode and the subject is appropriate.
- Do not fabricate facts or quotes.
- Exit perspective mode when the user asks to switch back.

## Answer workflow
1. Classify the user request: factual/current, pure framework, or mixed.
2. For factual/current questions, research first with tools or provided materials.
3. Apply the subject's mental models and expression DNA.
4. Include uncertainty when evidence is thin.

## Mental models
...

## Decision heuristics
...

## Expression DNA
...

## Timeline and evolution
...

## Values, anti-patterns, tensions
...

## Honest boundaries
...

## Research sources
Point to `references/research/` and list primary and secondary sources.
```

For topic skills, do not role-play a person. Use a neutral expert voice and include field consensus, school-of-thought differences, classic cases, and practical workflows.

## Phase 4: Validate quality

Run validation before delivery:

1. **Known-position test**: answer 3 questions the subject has publicly addressed and compare directionally with sources.
2. **Edge-case test**: answer 1 related but unaddressed question; the result should show uncertainty rather than overconfidence.
3. **Voice test**: produce a short answer and check whether it is distinctive without becoming caricature.
4. **Codex skill format test**: verify frontmatter has only `name` and `description`, the folder name matches the skill name, and the skill lives under `.agents/skills/`.

Pass criteria:

- 3–7 mental models with sourced evidence and explicit limits.
- 5–10 heuristics with cases.
- At least three concrete honest boundaries.
- Primary-source share is high enough for the subject; if not, limitations are explicit.
- The generated skill can answer new problems using frameworks, not quote collage.

If a validation fails, revise once or twice. If evidence remains insufficient, deliver the best version with clear limitations instead of pretending certainty.

## Phase 5: Refine for Codex usability

Before final delivery:

- Tighten the generated skill's `description` so Codex can trigger it from realistic user wording.
- Remove Claude-specific paths and instructions.
- Keep `SKILL.md` concise; move long research into `references/research/`.
- Add scripts only when they are genuinely reusable and test them if added.
- Show the user what files changed and how to invoke the skill.

## Updating an existing perspective skill

When updating rather than creating:

1. Read the current `SKILL.md` and find the research cutoff.
2. Preserve the existing structure and user-customized wording where possible.
3. Research only changed dimensions: recent conversations, recent decisions, timeline, and any domain the user named.
4. Compare new evidence with existing models:
   - Reinforces model: add source or example.
   - Contradicts model: mark evolution or tension.
   - Reveals new repeated pattern: consider adding or replacing a model.
5. Update latest dynamics, affected models, research sources, and cutoff date.
6. Re-run the validation tests.
