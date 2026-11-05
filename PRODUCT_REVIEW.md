# Product Review — Atlas // OS

**Verdict:** The vision is strong and has a clear point of view: *depth over breadth, active over passive, from first principles to production.* The biggest risk is not the idea, it is **scope and trust**. Ship one excellent, verified course slice first, then let the compiler scale it.

---

## 1. What's strong

- **Clear differentiation.** Long-form, unbroken curricula with real codebases and papers, not a pile of short videos or quizzes.
- **Lesson design is genuinely good.** Naive solution → why it breaks → production algorithm is how strong engineers teach.
- **Active learning built in.** Ghost-slot labs, Saboteur bug-hunts, and Teach-Back make the learner do the work.
- **Fan-out is a real UX idea.** Rabbit holes that merge back to your exact paragraph solve the "I don't know the prerequisite" problem.
- **Mobile-aware.** Ghost-slots avoid typing boilerplate on a phone.
- **Agent has a clear job.** Research, perceive, create: compounding value as it learns the learner.

## 2. Risks and fixes

| # | Risk | Why it matters | Fix |
|---|---|---|---|
| R1 | **Content volume.** 100+ deep lessons per course cannot be hand-written | Quality collapses or the project never ships | Course compiler + verification gates + human review queue (`COURSE_ENGINE_SPEC.md`). Launch with **one module (~12 lessons)** as the vertical slice |
| R2 | **Accuracy of generated technical content** | One wrong formula destroys trust in a learning product | See examples below. Add automated verification and provenance badges |
| R3 | **Agent cost and abuse.** 30 parallel pages, vision, voice, course generation | Cost per user can exceed revenue; free tiers get drained | Per-user and per-job budgets, small-model defaults, caching, visible cost meter |
| R4 | **Prompt injection from web pages** | An agent with tools that reads untrusted pages can be hijacked | Reader/actor split, tool permission tiers, egress allowlist (`SECURITY_AND_SAFETY.md`) |
| R5 | **Coding on phones.** In-browser Node runtimes are limited on mobile browsers | Labs may not run on some devices | Tiered sandboxes: Pyodide for light labs, server microVM for heavy ones. **Test on real iOS and Android early** |
| R6 | **"Atlas does everything"** | Unclear launch story, huge surface | Launch with 3 jobs: *Explain this*, *Build me a course*, *Quiz me*. Voice and camera come later |
| R7 | **Learning efficacy unproven.** No placement, retention, or assessment loop in the current vision | Completion ≠ learning | Placement diagnostic, spaced repetition, Teach-Back scoring, mastery model |
| R8 | **Licensing and copyright** for papers and code excerpts | Takedowns, legal risk | Link to originals, summarise, short quotes only, show code excerpts per repo license, pin permalinks |
| R9 | **Readability.** Prototype uses 9–10px mono labels and 13px body text | Long-form reading on a phone needs comfort | Min 11px for meta, 15–16px for lesson prose (`DESIGN_SYSTEM.md`) |
| R10 | **Name collision.** "Atlas" is used by well-known products (e.g. MongoDB Atlas, OpenAI's ChatGPT Atlas browser) | Hard to search, possible trademark friction | Do a trademark and app-store search before committing; keep "Atlas" as a working name |

### Accuracy examples from the prototype (why R2 is real)

- **Write-amplification formula.** The prototype shows `WAF = 1 + (T / B_l0) × L_levels`. That is illustrative, not a standard result. For leveled compaction, write amplification is commonly estimated as roughly proportional to **fan-out × number of levels** (order of `T × L`, depending on assumptions). The lesson must cite its source and state assumptions.
- **Line-number citations.** `db/version_set.cc#L3120` will drift and may already be wrong. Cite **commit-pinned permalinks** and verify the symbol exists at that commit.
- **Config defaults** (e.g. L0 slowdown/stop triggers) must be checked against the pinned version's source or docs.

Takeaway: every number, symbol, and file reference needs a source and an automated check.

---

## 3. Proposed additions

Priority: **P0** MVP, **P1** soon after, **P2** later. Effort: S/M/L.

| ID | Feature | What it does | Why it's worth it | Effort | Pri |
|---|---|---|---|---|---|
| A1 | **Placement diagnostic + adaptive skip** | 10–15 min adaptive quiz sets your starting node; known modules are collapsed | 100+ lesson courses need a way in; respects experienced learners | M | P1 |
| A2 | **Spaced repetition from Teach-Backs** | Every lesson yields review cards (FSRS scheduling) | Retention is the real outcome; cheap to add | M | P0 |
| A3 | **Deterministic Cluster Simulator** | In-browser seeded simulation of nodes, partitions, clock skew, message loss; replay any run | Makes Raft/Paxos/replication *felt*; unique, shareable | L | P1 |
| A4 | **Failure Museum** | Interactive replays of real outage post-mortems (timeline, metrics, "what would you have done?") | Principal-level judgment; highly engaging | M | P1 |
| A5 | **Provenance + confidence badges** | Each claim tagged *Verified (source)*, *Model-written*, or *Disputed*; tap to see sources | Trust is the product | M | P0 |
| A6 | **Agent Trace panel** | "Show your work": searches, pages read, tools used, cost | Trust, debugging, learning how research is done | S | P0 |
| A7 | **Interview Mode** | Voice system-design / coding interviews with a rubric and written debrief | Strong motivation hook; reuses voice + governor | M | P2 |
| A8 | **Paper-to-Lab** | Reproduce a paper's key result against target metrics | Bridges reading and building | L | P2 |
| A9 | **Offline packs + audio lessons** | Download a module; listen in Commute Mode | Phone-first reality | M | P1 |
| A10 | **Portfolio export** | Labs → GitHub repo; Proof cards; resume bullets | Concrete career value | S | P1 |
| A11 | **Atlas MCP server** | Expose Codex and skills to the learner's coding agent/editor | Makes learning useful in daily work | M | P2 |
| A12 | **Branch & merge for fan-out** | Side-quests are versioned branches; merge back with a summary | Formalises the core UX; enables sharing | M | P0 |
| A13 | **Study squads + lab PR reviews** | Small groups; peer review of lab solutions | Motivation and feedback at low cost | M | P2 |
| A14 | **Cost meter + budgets** | Show agent spend per task; user-set caps | Controls R3, builds trust | S | P0 |
| A15 | **Error reports → fixes → credit** | "Report an issue" on any block; fixes credited | Scales QA via the community | S | P1 |
| A16 | **Multilingual lessons** | Render lessons in other languages with technical terms preserved | Wider audience | M | P2 |
| A17 | **Accessibility mode** | Screen-reader math (MathML), reduced motion, captions | Inclusive by default | S | P1 |
| A18 | **Misconception detector** | Maps wrong answers and failed tests to a catalogue of known misconceptions → targeted remediation | Better than "try again" | M | P1 |

---

## 4. Defer from MVP

Voice and camera input · community forks · study squads · 13 TTS providers (use 1–2) · GPU labs · native apps (ship as a PWA first) · Pulse automation beyond a weekly curated item.

## 5. Open decisions

1. Who is the first learner? (fresher → backend/AI infra roles, working engineer going deep, or both?)
2. Which flagship module is the vertical slice? (LSM storage is a good candidate, since the prototype already covers it.)
3. Free-model-only, or paid models for course compilation?
4. Business model: free tier with budgets + Pro, or fully free/open-source?
5. Public community courses at launch, or curated-only?
6. PWA first, or straight to React Native (camera/voice)?
