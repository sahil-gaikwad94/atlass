# PRD — Atlas // OS

**Status:** Draft v0.1 · **Owner:** Founder · **Platforms:** PWA first, native later

## 1. Problem

Engineers who want deep skills (storage engines, distributed systems, inference, kernels) face two bad options: shallow content (short videos, listicles) or scattered primary sources (papers, huge codebases) with no path through them. Nobody holds their hand from first principles to production, and nothing adapts to what they already know.

## 2. Vision

> A living, personalised engineering university in your pocket. Open it, and you are always one tap from the next *right* thing to learn, a real system to dissect, a lab to build, and an agent that can research anything and build you a new course on demand.

## 3. Principles

1. **Depth over breadth.** No shortcuts, no hand-waving; derive it from first principles.
2. **Do, don't watch.** Every lesson ends with building or debugging.
3. **Trust through verification.** Claims have sources; code runs; numbers are checked.
4. **Adapt to the learner.** Bridge from what they already know; skip what they've mastered.
5. **Phone-first, not phone-only.** Great on a bus; great on a desk.
6. **Agent as colleague.** Transparent, budgeted, and interruptible.

## 4. Users

| Persona | Goal | Needs |
|---|---|---|
| **Career-builder** (fresher / early career) | Land backend or AI-infra roles | Clear path, portfolio artifacts, interview practice |
| **Deep-diver** (working engineer) | Master a subsystem at Staff level | Skip basics, real codebases, production case studies |
| **Student** | Understand CS/ML foundations properly | Intuition-first explanations, math decoder, labs |
| **Course author** (later) | Share expertise | Compiler tooling, review, publishing |

## 5. Product surface (4+1 dock)

| Tab | Purpose |
|---|---|
| **Courses** | Browse courses → phases → modules → lessons; progress; competency graph |
| **Studio** | Long-form reader + sandbox: fan-out, math decoder, simulators, X-Ray, labs |
| **✦ Atlas** | Agent: research, perceive (camera/voice/files), create courses and apps |
| **Pulse** | Daily frontier breakdowns, community courses, 60-second challenges |
| **Vault** | Codex (notes), /journey timeline, skills, proof cards, review queue |

## 6. Functional requirements

### Courses
- **FR-C-1** Course = Phases → Modules → Lessons; no fixed level count.
- **FR-C-2** Show per-module counts (lessons, labs, papers, X-Rays) and learner progress.
- **FR-C-3** Placement diagnostic can pre-complete or collapse known modules *(P1)*.
- **FR-C-4** Each course shows prerequisites and a cross-course competency graph.
- **FR-C-5** Content is versioned; learners see "updated since you read this".

### Studio (reader)
- **FR-S-1** Render typed blocks (see `COURSE_ENGINE_SPEC.md`) in a continuous scroll.
- **FR-S-2** Fan-out: any marked term opens an inline branch; closing merges back to the same scroll position.
- **FR-S-3** Math Decoder: tap a symbol → plain-English role, matching code line, live value with sliders.
- **FR-S-4** Simulators are embedded, interactive, deterministic, and pausable.
- **FR-S-5** X-Ray tours link to commit-pinned source and show a call-graph spine.
- **FR-S-6** "Bridge" callouts adapt to the learner's `USER.md` background.
- **FR-S-7** Every claim block exposes provenance (source list, verified/model-written badge).
- **FR-S-8** Reading position, highlights, and notes sync across devices; offline for downloaded modules *(P1)*.

### Labs
- **FR-L-1** Ghost-slot labs: boilerplate fixed, learner edits marked regions only.
- **FR-L-2** Automated tests grade results; failures return readable diagnostics.
- **FR-L-3** Saboteur labs inject a bug; learner finds and fixes it.
- **FR-L-4** 5-level Hint Ladder; each level reduces mastery credit slightly.
- **FR-L-5** Teach-Back: one-sentence explanation, scored against a rubric.
- **FR-L-6** Staff-review comment on a completed lab (style, complexity, edge cases).

### Agent Atlas
- **FR-A-1** Chat with streaming responses, tool-call visibility, cancel, and steer.
- **FR-A-2** `atlas.web`: search and read pages with citations; max parallelism per budget.
- **FR-A-3** `atlas.perceive`: images, PDFs, screen/lesson context; voice and video later.
- **FR-A-4** `atlas.create`: generate a course outline, then lessons/labs, as background jobs with progress.
- **FR-A-5** Every run shows a trace and cost; user-set budgets are enforced.
- **FR-A-6** Skills: agent proposes reusable skills; user can view, edit, disable.
- **FR-A-7** Context-aware: knows the lesson, selection, and current lab state.

### Vault and memory
- **FR-V-1** Codex: auto-generated Markdown notes per lesson, exportable as an Obsidian-compatible zip.
- **FR-V-2** `/journey` timeline of milestones and skill versions.
- **FR-V-3** Spaced-repetition queue built from Teach-Backs and key concepts.
- **FR-V-4** Proof cards: shareable page with lab results and verification hash.
- **FR-V-5** Memory controls: view, edit, delete anything the system remembers.

### Pulse
- **FR-P-1** Daily/weekly deconstruction of a frontier paper or system, with a short interactive.
- **FR-P-2** "Turn into a course" button sends the item to the compiler.
- **FR-P-3** Community courses: fork, version, report errors *(P2)*.

## 7. Non-functional requirements

| ID | Requirement |
|---|---|
| NFR-1 | Lesson first paint < 2 s on a mid-range phone over 4G; simulators lazy-load |
| NFR-2 | Agent first token < 2 s typical; long jobs run in background and survive app close |
| NFR-3 | Sandbox runs start < 5 s (Pyodide, warm) / < 15 s (server microVM) |
| NFR-4 | No user code runs outside an isolated sandbox |
| NFR-5 | Reading and review work offline for downloaded modules |
| NFR-6 | WCAG 2.2 AA contrast and touch targets; reduced-motion support |
| NFR-7 | Per-user monthly agent spend capped by plan and user setting |
| NFR-8 | Learner data exportable and deletable on request |

## 8. Success metrics

| Metric | Target for MVP slice |
|---|---|
| Lesson completion rate | ≥ 60% of started lessons |
| Lab pass rate (≤ 3 hints) | ≥ 50% |
| Teach-Back score ≥ 3/4 | ≥ 60% |
| D7 retention | ≥ 25% |
| Review-queue adherence | ≥ 3 sessions / week among retained users |
| Content error reports | < 1 per 1,000 lesson views, median fix < 48 h |
| Agent cost / active user / month | Within budget set in `ROADMAP.md` |

## 9. Out of scope (v1)

Live instructors, video hosting, certificates with third-party accreditation, enterprise SSO, GPU-heavy labs, marketplace payments.

## 10. Monetisation options *(decide later)*

Free tier with small agent budget → Pro (larger budget, offline packs, more sandbox time) → Teams (shared courses, progress dashboards). Creator revenue share is a later option.

## 11. Open questions

See `PRODUCT_REVIEW.md` §5.
