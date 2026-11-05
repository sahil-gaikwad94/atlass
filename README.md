# Atlas // OS — Technical Docs

**Status:** Draft v0.1 · 2026-10-18
**One line:** A mobile-first learning OS for deep engineering mastery: huge multi-module courses, a long-form interactive reader, and a sovereign multimodal agent that can research the web and build new courses on demand.

> **Source note.** These docs are built from the latest product message of the design chat (courses, Studio, Sovereign Agent Atlas, 4+1 dock) and the React prototype in it. Earlier chat history was not available, so some terms are *interpreted* (see the table below). Anything marked **[ASSUMPTION]** should be confirmed or edited.

---

## Doc map

| File | What it answers |
|---|---|
| `PRODUCT_REVIEW.md` | What's strong, what's risky, what to add. **Read this first.** |
| `PRD.md` | What we're building, for whom, success metrics, requirements |
| `ARCHITECTURE.md` | System design, components, flows, tech choices (ADRs) |
| `AGENT_HARNESS_SPEC.md` | How Agent Atlas runs: gateway, lanes, tools, sub-agents, skills |
| `COURSE_ENGINE_SPEC.md` | Course/lesson structure, fan-out, the compiler pipeline, quality gates |
| `LABS_SANDBOX_SPEC.md` | Ghost-slot labs, Saboteur bug-hunts, hint ladder, sandbox tiers |
| `MEMORY_PERSONALIZATION_SPEC.md` | USER.md, memory layers, learner model, spaced repetition |
| `DATA_MODEL_AND_API.md` | Database schema, REST endpoints, WebSocket events |
| `DESIGN_SYSTEM.md` | Tokens, type, components, motion, accessibility |
| `SECURITY_AND_SAFETY.md` | Threat model, sandbox isolation, prompt injection, licensing |
| `ROADMAP.md` | Phases, MVP vertical slice, metrics, kill criteria |

---

## Glossary

| Term | Meaning |
|---|---|
| **Course** | Full end-to-end curriculum on one domain (10–20+ modules, 80–150+ lessons) |
| **Phase** | Group of modules (e.g. Foundations → Storage → Consensus → Principal) |
| **Module** | A themed set of ~8–12 lessons, ends with a capstone or lab |
| **Lesson** | One long-form chapter made of typed **blocks** |
| **Fan-out branch** `⑂` | Inline rabbit-hole: a prerequisite or deep-dive that opens from any paragraph and merges back to the exact spot |
| **Block** | Unit of lesson content: Bridge, Narrative, FanOut, MathDecoder, Simulator, XRay, Lab, Review, TeachBack |
| **Math Decoder** | Interactive equation: tap a symbol → plain-English role + code line + live values |
| **X-Ray** | Guided tour through a real codebase's execution path, pinned to a commit |
| **Ghost-slot** | Lab where boilerplate is pre-written and the learner fills only the core lines |
| **Saboteur** | Sub-agent that injects a realistic bug for the learner to find |
| **Socratic Governor** | Policy layer that guides with questions and never leaks full solutions |
| **Hint Ladder** | 5 hint levels, each costing a little mastery credit |
| **Teach-Back** | One-sentence explanation the learner writes; scored and turned into review cards |
| **Gateway** | Persistent WebSocket server that runs agent sessions |
| **Lane** | Ordered execution queue inside a session (foreground, background, voice) |
| **Skill** | Reusable `SKILL.md` procedure the agent learns and refines |
| **Codex** | The learner's auto-written, Obsidian-compatible Markdown notes |
| **Proof card** | Shareable, verifiable record of a completed lab or module |

## Interpreted terms [ASSUMPTION]

The chat used names from outside projects. These docs **define behaviour independently**, so nothing here depends on those projects existing or staying the same.

| Term in chat | How these docs define it |
|---|---|
| Prometheus → Metis → Momus | Planner → gap/prerequisite analyst → adversarial reviewer (roles in the course compiler) |
| SimpleMem + Honcho + Skill-V | 5-layer memory: working, episodic, semantic/learner-model, procedural skills (versioned + verified), codex |
| git-context-controller | Branch/commit/merge semantics for agent context and fan-out side-quests |
| Fanout (reference) | Benchmark for course depth (12+ modules, 100+ lessons per track) |
| 13-provider TTS | Pluggable TTS adapter interface; launch with 1–2 providers |
| `~/.atlas/...` paths | Per-user *virtual* file tree stored server-side (not the device's real home folder) |

## Conventions

- **MUST / SHOULD / MAY** follow RFC 2119 meaning.
- Requirement IDs: `FR-` functional, `NFR-` non-functional, `AH-` agent harness, `CE-` course engine, `LB-` labs.
- Diagrams use Mermaid.
- Every spec lists open questions at the bottom.
