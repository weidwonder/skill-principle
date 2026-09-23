# skill-principle

*[中文文档](./README_zh.md)*

A best-practice principle library for Agent Skills, with two ways to use it: **building** — while you write a new skill or iterate on an existing one, it drives the structural decisions and forces a self-review at the end; **reviewing** — it reads a finished skill and turns "something feels off" into an actionable, graded P0/P1/P2 report. Both paths share one set of principles, so the guidance you build by and the bar you're judged against never drift apart. It always reads the way the agent that will actually use the skill does, so broken triggers, dangling references, and silent logic gaps get caught before they cost you a bad run in production.

> Non-goal: this skill does not write a whole skill scaffold for you (that's the job of skill-creator / darwin-skill and similar authoring tools) — the building path gives principles, structural decisions, and a self-review.

## Features

- **27 principles, written twice in one document**: each one states the principle, what to do when building, and how to judge it when reviewing (with its severity). The building path reads the first two parts, the review path reads the first and third — so a "building guide" and a "review checklist" can't drift apart over time
- **Two paths, routed automatically**: asking to write or improve a skill goes down the building path (intent & non-goals → frontmatter & description → structure & disclosure layers → content → mandatory self-review); asking for a review goes down the review path (confirm target → inventory the reference graph → judge item by item → graded report → optional deep modes). Given just a directory and no stated intent, it asks first
- **Principles in three layers**:
  - **Mechanical (1.1–1.4)**: frontmatter compliance (field whitelist, `name` rules), description trigger quality (keyword-stuffing thresholds, exclusion clauses), Markdown structural integrity, security baseline (destructive commands, hardcoded credentials, over-privileged `allowed-tools`)
  - **General (2.1–2.18)**: usage-only content (no engineering leftovers), workflow/tool completeness (including decision/completion criteria and script-example dry-runs), internal consistency, git version management & asset migration, conciseness & size thresholds, no orphaned docs & reference depth, no common-sense violations, example coverage, failure-scenario guidance, positive-example bias & accurate counter-example scope, implicit-logic gaps made explicit, runtime neutrality, goal/non-goal clarity, reference-loading conditions, structural consistency (terminology drift / near-duplication / structural breaks), progressive disclosure & attention control (layering, scenario splitting, task decomposition, step-by-step execution, per-step cognitive-load thresholds), single-home facts (each fact lives in exactly one place; everywhere else is a pointer, across files and across skills)
  - **Dependency & install (3.1–3.6)**: standalone install instructions, environment isolation, state/credential JSON separation (credentials must be gitignored), failure-driven install guidance, deployment cohesion (bundled CLIs/scripts and deploy docs must live inside the skill root, not beside it), and cross-platform installation with a deterministic location (prefer a dedicated directory inside the skill root)
- **Mandatory self-review when building**: a freshly written or edited skill gets run against the principles, P0/P1 fixed on the spot. The "conversational-constraint capture" sub-item is required here — constraints the user mentioned in passing while building, but that never made it into the skill, are the most common omission
- **Graded report**: every issue is pinned to a location (file:line) + problem + suggested fix, plus a list of passed items, N/A items, and an optional 8-dimension maturity scorecard
- **User-adjudication gate**: ambiguous findings (orphaned docs, suspected intentional deviations, requests to add hard "never do X" rules, narrowing counter-examples) are queued and presented to the user together at the end, instead of interrupting item by item
- **Large-skill splitting**: for skills with more than 20 files, review work is fanned out to parallel sub-agents by branch, while global principles (cross-file conflicts, orphaned docs, version management, structural consistency, progressive disclosure, deployment cohesion, cross-platform install) stay in the main session
- **Three optional deep modes** (off by default, offered after the review or self-review; model tailoring can also be requested directly):
  - **Enhanced review**: cross-checks against current domain practice online, flagging staleness / inconsistency / gaps / over-restriction
  - **Behavioral testing**: trigger-rate sampling on positive/negative cases, RED/GREEN/REFACTOR behavioral stress tests, meta feedback
  - **Model tailoring**: the target model itself trims the skill down to the delta between its own default behavior and what the author requires (external facts are always kept verbatim); an examiner model (a stronger model the user picks, or the target model itself) then writes an exam, and the target model's sub-agents sit it blind in three groups (original / tailored / optional no-skill baseline), and lost points drive restoration until the pass line is met. The original is never modified, and the variant is valid only for that model

## Install

Option 1: symlink (recommended, keeps you in sync with the repo)

```bash
git clone <this repo> ~/projects/skills/skill-principle
ln -s ~/projects/skills/skill-principle/skill ~/.claude（or codex etc..）/skills/skill-principle
```

Option 2: use the packaged `.skill` file under `dist/` (a zip archive) — unzip it into your runtime's skills directory as `skill-principle/`.

> `~/.claude/skills/` above is Claude Code's directory convention, shown only as an example; for any other runtime that supports Agent Skills, substitute its own skills directory.

## Usage

Just tell an Agent that supports Agent Skills:

Building:

- "I want to write a skill that turns meeting notes into a task list"
- "Help me restructure this skill"
- "My skill never triggers — how should the description be written?"

Reviewing:

- "Review ~/projects/skills/foo for me"
- "Check the quality of this skill"
- "Give me feedback on this SKILL.md"

The review path only edits the target skill if the user explicitly asks. The building path's self-review is the exception — it reviews what this very conversation just wrote, and fixes P0/P1 on the spot.

## Directory structure

```
skill-principle/
├── skill/                          # runtime skill package (symlink target)
│   ├── SKILL.md                    # path routing + building path + review path
│   └── references/
│       ├── principles.md           # 27 principles (principle / building / review)
│       ├── report-template.md      # graded report template + scorecard
│       ├── enhanced-review.md      # enhanced review mode
│       ├── behavior-testing.md     # behavioral testing mode
│       └── model-tailoring.md      # model tailoring mode
├── dist/                           # packaged build artifacts (.skill)
└── README.md
```

## Version

Versioning is managed via git tags; current version is `v0.10.0`.

- `v0.1.0` — Initial release: three-layer checklist, split review, adjudication gate, enhanced review
- `v0.2.0` — Ran a full self-review of the skill on itself (including a real sub-agent split run), fixed 3 P1 issues around split-responsibility alignment; added mechanical-layer checks; rewrote wording to be tool-neutral
- `v0.3.0` — Drew on darwin-skill, obra/superpowers, philschmid, skill-validator, and verdict/skillscore; added behavioral testing mode, security scanning, runtime-neutrality/non-goal/reference-loading-condition checks, scorecard
- `v0.4.0` — Logical-consistency hardening: for skills repeatedly edited by multiple LLMs/agents, added 2.16 structural consistency (terminology drift / near-duplication / structural breaks); 2.2 now also checks decision and completion criteria; 2.12's implicit-logic walkthrough now runs on all skills; scorecard gained a "logical consistency" dimension (8 total)
- `v0.5.1` — Deployment cohesion: added 3.5 (bundled CLIs/scripts and deploy docs must live inside the skill root; sitting beside it is a P0); sibling assets in the parent directory are now inventoried; section 3's execution condition widened to cover skills that bundle their own CLI/scripts
- `v0.6.0` — Attention engineering: added 2.17 progressive disclosure & attention control (layering, scenario splitting, task decomposition, step-by-step execution, per-step cognitive-load thresholds, disclosure-timing mismatch, context offloading); the scorecard's "reference design" dimension is now "reference design & progressive disclosure"
- `v0.7.0` — Cross-platform installation: added 3.6 requiring supported-platform coverage, deterministic installation locations, and preference for dedicated dependency directories inside the skill root
- `v0.9.0` — Single-home facts: added 2.18 (each fact has exactly one home; elsewhere write name + pointer — checks for multi-home duplication, out-of-scope expansion, missing pointers, dangling pointers, and a missing ownership/routing table); build path Step B3 now decides fact ownership and the routing table; the skill was also refactored against 2.18 — the two annotation lists and the "core stance" each now live in exactly one place, and SKILL.md gained an ownership table
- `v0.10.0` — Model tailoring: added a third deep mode that trims a skill for one specified model (external facts are always kept), measures the loss with a blind three-group exam (original / tailored / no skill), and restores cut content from the lost points
- `v0.8.0` — Renamed `skill-reviewer` → `skill-principle` and extended from reviewing to building: the review checklist was reworked into a principle library written as principle / building / review (`references/principles.md`); SKILL.md gained path routing and a building path (Steps B1–B5, ending in a mandatory self-review); the review flow is unchanged (Steps R1–R5)

## Design references

- [darwin-skill](https://github.com/alchaincyf/darwin-skill) — review dimensions, runtime hygiene, scorecard
- [obra/superpowers](https://github.com/obra/superpowers) — RED/GREEN/REFACTOR sub-agent behavioral testing method
- [Testing Claude Skills](https://www.philschmid.de/testing-skills) — trigger-rate positive/negative sample evaluation
- [skill-validator](https://github.com/agent-ecosystem/skill-validator) — graded token thresholds, keyword-stuffing detection
- [Anthropic Agent Skills Best Practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices) — progressive disclosure, avoiding time-sensitive information
