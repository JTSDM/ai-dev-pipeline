# ai-dev-pipeline

> A complete orchestration pipeline for AI-assisted development: **Grill → Spec → Tickets → Implement → Review**

**The deliverable is a file, not a chat log.** There is no step in this pipeline called "write code" — the quality of planning determines the quality of AI output.

---

## Credits & Provenance

The **methodology comes from public work; the engineering is this project's contribution.** The boundary is drawn explicitly so citations stay honest:

| Part | Origin |
|------|--------|
| The five core techniques (grill, ubiquitous language, TDD, deep modules, design-the-interface) | **Matt Pocock**, AI Engineer Summit talk *Software Fundamentals Matter More Than Ever* |
| Pipeline shape (grill → spec → tickets → implement → review) | Community project `mattpocock/skills` (v1.1) |
| "Long-context models make rewind-and-retry nearly free, so planning is the highest-leverage step" | The "Waterfall 2.0" argument |
| Underlying design principles | Brooks, *The Design of Design*; Evans, *Domain-Driven Design*; Ousterhout, *A Philosophy of Software Design*; *The Pragmatic Programmer* |
| **L/M/S tiering, dual-zone artifact storage, doc↔skill single-source dependency, bidirectional rollback with mini-Grill, review disposition table, production validation** | **Original to this project** |

If you cite the methodology, cite Matt Pocock's original talk. If you cite the orchestration and engineering design, cite this project.

## The Problem

AI doesn't write bad code because it's bad at code. It writes bad code because software fundamentals got dropped. Symptoms:

- One sentence of requirements → the model starts writing, filling gaps with "plausible guesses"
- Bug appears → rewrite the prompt and regenerate → software entropy, worse every cycle
- Key decisions live only in the chat log → gone when you switch windows or models
- No test constraint → the tests the AI writes will only agree with what it already produced

## Core Mechanisms

| Mechanism | Description |
|-----------|-------------|
| 5-step pipeline | Grill → Spec → Tickets → Implement → Review. No step called "write code" |
| L/M/S tiering | Pick the tier by *cost of being wrong*. The S tier must stay light, or the process gets bypassed |
| Artifacts are files | DECISIONS / SPEC / TICKETS all land on disk — survives model switches and context clears |
| TDD as control loop | Write the failing test first. Tests are the cheapest oracle you have |
| Ubiquitous language | Generate a project-specific `CONTEXT.md` glossary so the AI stops rambling and guessing |
| Bidirectional rollback | Spec-level errors roll back to Spec; implementation-level errors roll back to Implement |
| Disposition table | With external review, produce a 采纳 / 有据反驳 / 不在本编号 classification — no silent fixes |

## Install

Drop the `ai-dev-pipeline/` directory into your skills folder:

- **WorkBuddy / Claude Code**: `~/.workbuddy/skills/` or `~/.claude/skills/`
- **Project-level**: `{workspace}/.workbuddy/skills/`

Then invoke it in conversation (`@ai-dev-pipeline`, or route it through your own skill selector).

## Usage: Three Tiers

| Tier | Fits | Pipeline |
|------|------|----------|
| **L — large** | New project / new module / architectural decision | Full Grill → Spec → Tickets → Implement → Review |
| **M — medium** | Single feature / cross-file change | 10-question self-check (skip if all defaultable) → Spec → Tickets → Implement → Review |
| **S — small** | Bug fix / copy change | One-line task description (with acceptance criteria + test) → Implement → Review |

Selection rule: **pick by cost of being wrong.** Ambiguous requirements, multiple files, or any data-model / interface design → at least M. When unsure, go one tier up.

## Workspace Convention

- **Process zone**: `.ai-workflow/` at project root (gitignore it). All process files start here
- **Long-term asset zone**: `docs/ai-workflow/` (committed to Git). Migrate durable content here after Review

## Layout

```
ai-dev-pipeline/
├── SKILL.md                          # The single source of truth for how it runs
└── references/
    ├── docs/
    │   └── workflow-ai-dev-pipeline.md  # Methodology overview (why / when / pitfalls)
    ├── grill-questions.md             # Three-layer question bank
    └── templates/
        ├── CONTEXT.md                 # Project glossary template
        ├── DECISIONS.md               # Decision log template
        ├── SPEC.md                    # Spec template
        └── TICKETS.md                 # Ticket list template
```

Note: the bundled documents are written in Chinese. Pull requests adding English translations are welcome.

## Doc ↔ Skill Single-Source Rule

`SKILL.md` is the only operational source of truth — change it and the process changes. The overview document holds principles only (why / when / pitfalls) and never duplicates steps, templates, or paths, which prevents write-drift between two copies. Sync the overview only when **process principles, tiering thresholds, lifecycle rules, or methodology attribution** change.

## Validation

Validated on a real, in-production project (an education-domain online exam platform — not a demo):

- **M tier** (data-export feature, front-to-back): 13/13 smoke tests passed + frontend build passed + regression suite passed
- **S tier** (frontend routing edge-case fix): 6/6 logic tests passed + Review completed in 2 minutes

Two things worth noting: the S tier compressed down to "one-line description then implement," which shows the lightweight tier doesn't hollow out the process; and the M tier still produced verifiable acceptance criteria from the 10-question self-check alone, without a full SPEC.

## License

MIT — see [LICENSE](LICENSE).
