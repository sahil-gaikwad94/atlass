# Labs and Sandbox Spec

**Status:** Draft v0.1
**Scope:** Lab types, ghost-slots, Saboteur, hint ladder, grading, sandbox tiers, resource limits, the Cluster Simulator.

---

## 1. Lab types

| Type | What the learner does | Typical time |
|---|---|---|
| **Ghost-slot build** | Fill marked regions of a pre-written program (e.g. the K-way merge in a compactor) | 10–25 min |
| **Saboteur bug-hunt** | Find and fix a realistic injected bug using logs/tests/tools | 10–30 min |
| **From-scratch capstone** | Build a component end-to-end (e.g. mini LSM engine, mini Raft) | hours (multi-session) |
| **Paper-to-lab** *(P2)* | Reproduce a paper's key result against target metrics | hours |
| **Chaos drill** | Operate a simulated system under failure and write a post-mortem | 20–40 min |

## 2. Lab manifest

```yaml
id: lab_5_3_memtable_skiplist
title: Implement a SkipList MemTable
tier: pyodide                  # pyodide | webcontainer | microvm
language: python
estimated_minutes: 20
starter:
  files: [skiplist.py, tests/test_skiplist.py]
  ghost_slots:
    - file: skiplist.py
      id: insert_core
      lines: [28, 41]          # only these lines are editable
      description: "Insert with random level promotion"
tests:
  visible: [test_insert_lookup, test_ordering]
  hidden:  [test_duplicate_keys, test_level_distribution]
reference_solution: solutions/skiplist_ref.py   # never sent to client
limits: { cpu_seconds: 5, memory_mb: 256, output_kb: 64 }
hints:
  ladder: lab_5_3_hints.yaml
mastery_tags: [lsm.memtable, data-structures.skiplist]
saboteur_variants: [off_by_one_level, tombstone_dropped_early]
```

## 3. Ghost-slots

- Only the declared line ranges are editable; the rest is read-only and collapsed by default on mobile.
- Each slot shows the **contract** (inputs, outputs, invariants) above it.
- Mobile aids: code-token toolbar (brackets, operators, common identifiers), snippet chips from the lesson, undo/redo, one-tap "run tests".
- Syntax and type diagnostics via a light in-browser language service where available.
- Learners may expand a slot to a full-file edit after completing the lab ("free mode").

## 4. Saboteur

A sub-agent (or a pre-built variant) injects **one** bug into a working program.

Requirements:
- **LB-1** The bug must be realistic (off-by-one, wrong comparator, missed tombstone, race, wrong quorum count, timeout unit mix-up).
- **LB-2** Each variant fails *specific* tests and passes all others; verified by the compiler (CE-3).
- **LB-3** Learner gets observability tools: logs, a failing-test report, and a tracing/step view.
- **LB-4** Variants can be randomised per attempt to prevent copying solutions.
- **LB-5** After success, show the "diff of the bug" and a short post-mortem write-up.

## 5. Hint Ladder

| Level | What the Tutor may do | Mastery cost |
|---|---|---|
| **H1** | Ask a guiding question | 0 |
| **H2** | Point to the relevant concept or earlier lesson | small |
| **H3** | Narrow to the function or condition involved | small |
| **H4** | Give pseudo-code for the idea (no real code) | medium |
| **H5** | Reveal a partial scaffold for one sub-step | large |

Rules: no level reveals a full solution; the Governor logs level used; solution-leak detector compares output against the reference (token and AST similarity) before sending.

## 6. Grading and feedback

1. Run visible + hidden tests.
2. On failure: translate raw errors into plain language, show the smallest failing case, and point to the relevant lesson block.
3. On success: complexity/style review (Staff-review comment), Teach-Back prompt, create review cards, update mastery.
4. Misconception mapping (`PRODUCT_REVIEW.md` A18): known failure patterns map to targeted remediation.

## 7. Sandbox tiers

| Tier | Runtime | Best for | Limits and notes |
|---|---|---|---|
| **T-A: Pyodide** (in browser) | Python + common scientific libs in WebAssembly | Algorithms, small simulations, math | No server cost; first load is heavy (cache it); runs in a Web Worker with timeouts |
| **T-B: WebContainer-style** (in browser) | Node-like runtime | JS/TS labs | **Not reliable on all mobile browsers — verify before depending on it** |
| **T-C: Server microVM** | Short-lived isolated VM per run | Rust, Go, C/C++, databases, multi-process | Costs money; cold start; strict quotas |
| **T-D: GPU** *(P2)* | Provisioned GPU job | Inference/kernels labs | Tight quotas, queued |

The manifest declares a preferred tier and a fallback. The client picks the best tier it supports.

## 8. Sandbox security and limits

- No network by default; allowlist per lab (e.g. package mirror).
- Read-only base image; writable scratch dir with size cap.
- CPU/memory/time/output limits; kill on breach; per-user concurrency and daily quotas.
- No access to metadata endpoints, host filesystem, or other users' data.
- Secrets never enter the sandbox; test harness runs outside the learner's code process where possible.
- Logs sanitised before display.
- Details in `SECURITY_AND_SAFETY.md`.

## 9. Cluster Simulator (Distributed Systems labs)

A deterministic, seeded, in-browser simulator used by Raft/Paxos/replication modules.

**Capabilities**
- N nodes with pluggable logic (learner-implemented or reference).
- Virtual clock; per-node clock skew and drift.
- Network model: latency, loss, duplication, reordering, partitions.
- Fault injection: crash/restart, slow disk, pause.
- **Replay:** same seed + same inputs → same execution; share a run as a link.
- Visualisation: message timeline, node state, log replication view, invariant monitors (e.g. "at most one leader per term").
- Checkers: linearizability-style checks for simple registers; custom invariant hooks.

**Interfaces (sketch)**

```ts
interface Node { onMessage(msg: Msg, ctx: Ctx): void; onTimer(id: string, ctx: Ctx): void; }
interface Sim { seed: number; step(): Event; run(until: Predicate): Trace; inject(f: Fault): void; }
```

## 10. Telemetry

Per attempt: time to first run, runs per attempt, hint levels, failing tests, final pass, solution similarity score. Used for difficulty tuning and error discovery (labs with abnormal failure patterns are flagged).

## 11. Open questions

- Which microVM provider and what free-tier quota per learner?
- Do we allow learners to bring their own editor (open the lab locally via a CLI)?
- How do we detect and discourage copying solutions from the web?
