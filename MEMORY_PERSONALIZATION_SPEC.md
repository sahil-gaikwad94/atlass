# Memory and Personalization Spec

**Status:** Draft v0.1
**Scope:** What Atlas remembers about a learner, how it's stored and retrieved, the mastery model, spaced repetition, the Codex, and privacy controls.

---

## 1. Goals

- Make every lesson feel written for *this* learner (Bridge blocks, difficulty, pacing).
- Remember across sessions without leaking irrelevant or sensitive details into answers.
- Keep the learner in control: everything stored is viewable, editable, and deletable.
- Measure learning (mastery and retention), not just completion.

## 2. Memory layers

| Layer | Name | Contents | Lifetime | Store |
|---|---|---|---|---|
| **L1** | Working | Current turn context, open lesson/lab state | Minutes | In-process |
| **L2** | Episodic | Compressed summaries of sessions, branches, lab attempts | Weeks → months | Postgres + vectors |
| **L3** | Semantic / learner model | Stable facts and inferences: background, goals, strengths, misconceptions, preferences | Long-lived, revisable | Postgres + vectors |
| **L4** | Procedural | Skills (`SKILL.md`), versioned and tested | Long-lived | Postgres / object storage |
| **L5** | Codex | Human-readable Markdown notes the learner owns | Permanent until deleted | Object storage (virtual tree) |

### Rules
- **Write path:** after each run, a consolidator proposes L2/L3 updates; each memory has `source`, `confidence`, `created_at`, `last_used_at`.
- **Read path:** retrieval is **purpose-bound**. A memory is used only if it changes the answer (e.g. a Bridge analogy for a language the learner knows). Otherwise it stays out.
- **Sensitive data:** the system does not store sensitive personal categories (health, finances, identity documents, etc.) in L3 even if mentioned. Learning-relevant facts only.
- **Inference vs. statement:** inferred traits are labelled `inferred` and shown to the learner for confirmation; they never drive high-impact decisions alone.
- **Decay:** low-confidence, unused memories expire; contradicted memories are replaced with history kept in L2.

## 3. USER.md (the learner profile)

A short, human-editable Markdown file that seeds personalization.

```markdown
---
name: <display name>
level: intermediate            # self-declared; refined by placement
languages: [python, javascript]
goals: ["backend / infra roles", "understand storage engines deeply"]
time_per_day_min: 45
preferences:
  explanations: plain-first    # plain-first | math-first | code-first
  pace: steady
  voice: off
---

## Background
Free text the learner writes.

## Mental models I already have
- git (commits, rebase)
- REST APIs
```

- Created during onboarding (short, skippable); editable any time.
- Drives: Bridge blocks, default explanation order, daily goal, reminder timing.
- Agent may *suggest* edits ("add: comfortable with Docker?") but cannot change it silently.

## 4. Learner model and mastery

### Concept graph
Every lesson/lab/checkpoint is tagged with `mastery_tags` (e.g. `lsm.compaction.leveled`). Tags form a graph with prerequisite edges, shared across courses (cross-track credit).

### Mastery score
Per learner × concept: a probability-like score `m ∈ [0,1]` with `uncertainty`.

Inputs and rough weights **[ASSUMPTION — tune with data]**:

| Signal | Effect |
|---|---|
| Checkpoint correct (first try) | strong + |
| Lab pass without hints | strong + |
| Lab pass with hints | + reduced by hint level (see Hint Ladder) |
| Teach-Back rubric score | + / − |
| Review-card success after delay | strong + (and sets stability) |
| Failure mapped to a misconception | − and flags remediation |

Start simple (weighted Elo-style / Bayesian update). Move to a fuller knowledge-tracing model only when there is enough data to justify it.

### Uses
- **Next best action:** pick the lesson, review, or lab with the highest expected learning gain.
- **Placement:** an adaptive quiz sets initial scores; modules above a threshold collapse.
- **Honest progress:** the competency graph shows *mastered / shaky / untested*, not just "completed".

## 5. Spaced repetition

- Cards are generated from: Teach-Backs, checkpoints, key invariants, "why" questions, and short code-reading prompts.
- Scheduling: an open-source **FSRS**-style scheduler (per-card stability, difficulty, retrievability).
- Daily queue is capped (default 15–25 cards) and sorted by value; "catch-up" mode after a break.
- Card types: cloze, short answer (graded by the Tutor), "predict the output", "spot the bug".
- Review results feed mastery (§4).

## 6. Bridge-from-what-you-know

**Input:** learner's L3 facts + lesson concept tags.
**Process:**
1. Select up to 2 known mental models relevant to the concept.
2. Generate a short analogy that states where the analogy **breaks** (important for correctness).
3. Cache per (concept, mental model) so repeated generation is cheap.
4. If no confident match, skip the block rather than force an analogy.

Bridge blocks carry `status: model_written` unless reviewed.

## 7. Codex (L5)

- Virtual tree: `/atlas/codex/<course>/<module>/<lesson>.md`.
- Auto-created after each lesson: key ideas in the learner's own Teach-Back words, linked concepts (`[[wikilinks]]`), best lab code, and "mistakes I made".
- Obsidian-compatible: Markdown + front-matter + wikilinks; export as a zip any time.
- Learner can edit; edits are never overwritten by the system (system writes to a `generated` section, learner owns the rest).

## 8. `/journey`

A timeline of: lessons mastered, labs passed, skills evolved (with versions), course branches taken, proof cards. Filter by course or concept.

## 9. Privacy and control

| Control | Behaviour |
|---|---|
| **View** | Memory screen lists L2/L3 items with source and last used |
| **Edit / delete** | Per item and "delete everything" |
| **Pause** | Toggle to stop memory writes without losing existing data |
| **Export** | JSON + Codex zip |
| **Scope** | Memory is per learner; never mixed across accounts; not used to train shared models unless explicitly opted in |
| **Transparency** | Agent Trace shows which memories were retrieved for a run |

## 10. Evaluation

- **Relevance:** does a retrieved memory change the answer? (counterfactual test: answer with/without)
- **Harm checks:** memory is never surfaced in contexts where it would be surprising or sensitive.
- **Learning impact:** A/B placement vs. no placement; SRS vs. none; Bridge vs. generic, measured on retention and lab pass rate.

## 11. Open questions

- Do we use a hosted learner-modeling service or build our own consolidator?
- Cross-device conflict resolution for Codex edits (last-write-wins vs. merge)?
- Minimum data before enabling adaptive skipping by default?
