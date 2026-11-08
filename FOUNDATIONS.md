# Harness Engineering Foundations

Revision 5. First principles for how we build harnesses and AI-based software. Derived from two sources read in full on 2026-11-07, then reviewed against them by Codex over multiple rounds (review/codex-round*-findings.md) and revised each time.

- **P** = Barbaste et al., *Harness Engineering: Anatomy, Architecture, and Evolution of Coding Agents* (arXiv 2609.00006, July 2026). Source-code audit of 11 production coding harnesses, 13 observations, 29 patterns, 18 recommendations. Scope: coding agents. Snapshot: July 2026.
- **G** = Huashu, *Harness Engineering: The Complete Guide* (v260411, April 2026). Practitioner synthesis of the OpenAI Codex team, Hashimoto, Anthropic, Stripe, LangChain, Kent Beck. Snapshot: April 2026.

Where the two snapshots conflict on inventory, P supersedes G. Known supersessions: G §15 says Codex CLI has no hooks and G §16 lists no Codex automatic memory; P §10.2 documents Codex lifecycle hooks and P §9.6 its autonomous memory pipeline.

Two kinds of claim are kept apart throughout (P §14.5): **structural** claims (loop taxonomy, subsystem anatomy, the absences) are expected to endure; **inventory** claims (counts, thresholds, who ships what) are dated and decay in weeks.

Nothing here is verified against our own code. This is the foundation layer, not an audit.

---

## 0. Definition and scope

**Agent = Model + Harness.** The harness is everything except the weights: loop, tools, context, safety, orchestration, extension surfaces, and the interface layer that drives it (P §2.1). Prompt ⊂ Context ⊂ Harness (G §03). We are harness engineers: when the agent is wrong, we fix the harness, not the output (G §18, "on the loop").

**Gate zero: does this need an agent?** If a single model call or a fixed workflow solves it, do not build an agent loop. Agents are for open-ended problems where the number of steps cannot be predicted (G §17, quoting Anthropic). Same logic one level up: if one agent suffices, do not build multi-agent.

**Domain scope.** P's 18 recommendations are written for coding agents and P states its retrieval conclusions are domain-specific (P §13.2, §16). G's case studies are also coding. Everything below is therefore coding-evidenced. For ops and research harnesses, every default here (tool set, orchestration limits, retrieval choice, §7 requirements) is a hypothesis to test against that domain's task set, not a settled rule. Items marked **[coding-specific]** are the ones most likely not to transfer.

---

## 1. Axioms

Each axiom: claim → evidence → how we operate. Confidence is stated where the sources themselves hedge.

### A1. Loop sophistication has not been shown to predict task success. It does predict production readiness.
- Evidence: Mini-SWE-Agent's ~100-line linear loop self-reports SWE-Bench Verified in the same range as million-line systems. P explicitly leaves the matched-condition comparison open (P Obs 1; §13.4 Axis 1: "an open question that this study does not answer"). Most production mass is safety, UX, extensibility, transport.
- Operate: start every new agent as a linear `while` loop. P Rec 3 says begin with **bash only** and add tools per observed failure; P's own Listing 3 chooses a four-tool surface (bash, read, write, search_replace) as a pragmatic scaffold default. We adopt Listing 3's four as the starting point and treat that as our choice, not P's recommendation. Spend scaffold budget on safety, recovery, cost, and verification, never on loop cleverness.

### A2. The harness is the lever on a fixed model. Structural changes appear more portable than prose (suggestive, one ablation); prompt structure still matters.
- Evidence: LangChain, same GPT-5.2-Codex, Terminal Bench 52.8% → 66.5%, Top 30 → Top 5, by changing system prompt (vague guidance → mandatory four-stage Plan/Build/Verify/Fix workflow), tools, and middleware (G §10). AHE ablation: tools +3.3pp, middleware +2.2pp, long-term memory +5.6pp, system-prompt-only tuning −2.3pp; P reads this as "suggestive evidence that structural scaffold is more portable than prose-level prompt strategy" (P §15.5).
- Operate: when results are bad, first ask "what is missing: tool, guardrail, doc, environment info?" (G §06 Principle 2). Reach for structure (tools, middleware, hooks) before prose. When touching the prompt, change its workflow structure, not its adjectives.

### A3. Hand-rolled loops by default. Deterministic retrieval over embeddings for code. **[coding-specific]**
- Evidence: 0/11 production harnesses import an agentic framework in the runtime; 0/11 use vector RAG over source code; all use ripgrep, glob, tree-sitter, Markdown context files (P Obs 9, Rec 8, 15, 16). For conversation memory the picture is mixed: Hermes runs lexical SQLite FTS5 at scale with "no LLM calls anywhere", while OpenClaw's default memory plugin runs hybrid sqlite-vec KNN + FTS5/BM25 with embeddings on by default, for chat recall only (P §13.2, Table 13).
- Caveat: the zero-framework count is a corpus observation, not proof the alternative cannot work. LangChain's Deep Agents harness is built on LangGraph and converges on the same conventions (P §2.1 fn 1, §14.2). We choose hand-rolled as a default for debuggability and prompt transparency (P §13.2), not as a law.
- Operate: raw SDK calls or a harness SDK (Claude Agent SDK, openai-codex, openhands-sdk). No general agentic framework in the runtime unless a measured reason overrides. No embedding index over source code unless it first beats ripgrep + tree-sitter on a held-out task set (P Rec 16, verbatim condition). For conversation and note recall, lexical search (FTS5/BM25) is the default; hybrid embeddings are permitted when a held-out comparison on our own recall tasks shows a win.

### A4. Suggestions are not constraints. Enforce the bottom line mechanically, and decide who authorizes everything else.
- Evidence: instruction files are advisory; hooks, linters, CI, sandbox are deterministic (G §15). Policy-as-code in 6/11 systems. Hermes keeps a 12-pattern hard floor that survives `--yolo`, with the bypass flag frozen at import so injected content cannot flip it (P §10.7, Rec 11). For developer-tool contexts P Rec 9 prescribes a three-mode approval system (PLAN / DEFAULT / YOLO) with permission-scope patterns; for shared or automated contexts P Rec 10 prescribes OS-level sandbox, policy-as-code, and per-agent audit trails.
- Operate: three layers, all present. (1) Hard floor: catastrophic commands blocked regardless of mode. (2) Approval model: read-only plan mode, default ask-on-consequential, explicit bypass mode, with scope patterns on commands, paths, and URLs. (3) Policy as data: rules live in config files, not in loop code, and are auditable independently of the loop.

### A5. Instruction files are maps, not manuals. Mistake-driven for conventions; pre-seeded for red lines.
- Evidence: OpenAI AGENTS.md ~100 lines; Boris Cherny's CLAUDE.md ~100 lines; bloated files cause rules to be ignored; ETH Zurich found AGENTS.md can hurt (G §05, §14). Hashimoto: every line maps to a past agent mistake (G §07). G §05's keep-list includes "critical safety and quality red lines" alongside observed mistakes; G §15 says architectural constraints "must come first." Policy is migrating from prompt prose to configuration as models internalize norms (P Obs 3, §14.5).
- Operate: start near-empty. Conventions and style enter only after an observed failure. Safety red lines and architectural invariants are seeded on day one and belong in constraints (A4), not prose. Delete-test each remaining line: "would removing it cause a mistake?" Write what the agent cannot infer: commands, non-standard conventions, what does NOT exist here. Never version numbers, current tasks, or things readable from code. Root file ~100 lines with pointers out; router pattern when workspaces diverge (G §14).

### A6. Unsupported self-assessment is unreliable. Give the agent tool-grounded verification, and add independent review where stakes justify it.
- Evidence: the most common failure is "wrote a solution, re-read its own code, confirmed it looks ok, and stopped" (G §10). The guide's remedies are two: give the agent a way to verify its own work with real tools (Boris's #1 tip, 2–3× quality, G §08; Codex agents compare screenshots and logs then decide, G §06), and separate the evaluator from the generator, since "engineering a rigorous independent evaluator is far easier than teaching a generator to self-critique" (G §08). P documents outer verification loops in OpenHands (/goal judge) and Hermes (verify-on-stop) (P Table 12). G §06 and Boeckeler warn that internal-quality checks miss functional correctness.
- Operate: (1) Acceptance criteria before implementation: a plan plus testable success criteria, agreed before code is written (the Sprint Contract pattern, G §08). (2) Eyes: tests, browser, logs, screenshots the agent runs itself. (3) Pre-completion check against the criteria, not against confidence (LangChain PreCompletionChecklist, G §10). (4) For anything that ships, an independent reviewer in a fresh context, and a human who understands the change. A claim of "passing" is false until the check has run on current code.

### A7. Context is a scarce, degrading resource. Ration it with thresholds.
- Evidence: context rot; context anxiety in Sonnet 4.5 requiring full resets (G §05). Threshold compaction converged across 7/11 systems; preserve a verbatim recent tail; merge summaries incrementally (P Rec 7). Deferred loading is near-universal for **skills** (8/9 adopters) and growing for **tools** (1 → 3 systems in one quarter); P Rec 4 sets the tool threshold at ~15 (P Obs 8, §14.5, Rec 4). Reasoning sandwich (xhigh → high → xhigh) beat full-xhigh by 12.6pp on timed tasks because full throttle timed out (G §10).
- Operate: tool count past ~15 → deferred loading. Skills always metadata-eager, body on demand. Compaction thresholds are tunable defaults, not laws: fire at a fixed token buffer below the window, keep a verbatim recent tail, merge summaries rather than re-summarize, fire the same routine on overflow errors (specifics in Decision Rule 5). Allocate reasoning by phase when tasks are time- or cost-bounded. Kitchen-sink sessions are a bug; after two failed corrections, start a fresh session (G §16).

### A8. Memory's frontier is the write path. Take an explicit position, including deliberate absence.
- Evidence: four governance models coexist: agent-maintained (Codex, sandboxed consolidation over a git-baselined store), human-gated inbox (Gemini CLI), model-direct-but-bounded (Hermes: 2,200-char MEMORY.md frozen per session for cache hygiene; Claude Code, OpenHands context-file conventions), pre-turn recall sub-agent (OpenClaw). Pi and Mini-SWE-Agent deliberately persist nothing beyond the session substrate (P Obs 5, §9.6).
- Operate: memory is a set of **write paths** and **read paths**, and the paths may coexist (Gemini CLI has both direct model edits to GEMINI.md and a human-reviewed patch inbox, P §9.6). For every write path, the harness documents `{writer: model | background agent | human; reviewer: none | human | sandboxed agent; store: file | db; bound}`. "None" is a valid position and is documented as such. For read paths (startup injection, pre-turn recall sub-agent, lexical search tool), document what is injected and when. Three read-path policies, by how content reaches the model, not by storage medium. **Static startup snapshot** (content placed in the cached system-prompt prefix, such as Hermes's MEMORY.md/USER.md): bounded by characters and frozen for the session, so mid-session writes hit disk without invalidating the prompt cache (P §9.6). **Dynamic per-turn content** (pre-turn recall sub-agent output, session guidance, scratchpad; P §7.2 lists these as "computed per-turn"): placed after the cache boundary, bounded per turn, refreshed each turn by a stated rule. **On-demand reads** (search tool, ranked recall, Codex's citation-tracked artifacts): the durable store is unbounded, indexed, and scanned. Memory writes are an injection surface: scan them (A10). Repo is the single source of truth for project knowledge: if the agent cannot see it, it does not exist (G §06 Principle 1).

### A9. Single-agent by default. Multi-agent only for named cases, with a containment contract.
- Evidence: multi-agent ≈15× chat tokens; "most coding tasks involve fewer truly parallelizable tasks than research"; observed coordinator-worker use is mostly parallel codebase research, not parallel implementation (P §15.1, Rec 12). G's scenario table separately endorses: manual multi-session for independent modules, writer/reviewer dual sessions, and Planner/Generator/Evaluator for green-field full-stack builds (G §17). The $9 single-agent vs $200 three-agent comparison also changed elapsed time (20 min vs 6 h) and spend, so it shows the three-agent configuration produced a working result where the single run did not; it cannot isolate the evaluator's contribution (G §08). Anthropic removed Sprints and per-Sprint evaluation when Opus 4.6 arrived (G §05, §17). Sub-agent coordination: 8/9 systems use in-process primitives or, where they cross processes, something other than the standard protocols (P Obs 11, Table 14). Containment: Codex sub-agents inherit the parent's sandbox and execution policy (P §11.3); Hermes requires that a sub-agent "must not gain tools the parent lacks" (P §11.7).
- Operate: one agent until one of these holds: (a) a concrete breadth-first exploration phase where parallel context isolation beats serial search; (b) independent review of work that ships; (c) independent, non-coupled modules each in its own worktree; (d) a green-field build that exceeds what the current model completes reliably alone, re-evaluated at each model upgrade. Two forms, not interchangeable. **Human-orchestrated parallel sessions** (case c, and optionally b): separate top-level sessions, one per worktree, the human switches, reviews, merges (G §17, Boris's pattern); no runtime coupling. **In-process sub-agents** (cases a, b, d): children of one runtime, bound by a containment contract: a child inherits the parent's sandbox, approval mode, and policy, and never gains a tool or permission the parent lacks (P §11.3, §11.7). Sub-agents return summaries, never transcripts; durable results go to files to avoid the game of telephone. Sub-agents stay in-process (P Rec 13 scopes this to coordination within one runtime). No parallel implementation of coupled code in either form.

### A10. Skills for knowledge, MCP for external processes. Treat everything the agent reads as untrusted.
- Evidence: skills 9/11 vs MCP 8/11; deferred loading in 8/9 adopters; conditional activation by path or environment; registries, trust tiers, quarantine, agent-authored skills (P Obs 8, Rec 14, 17). Hermes scans context files, memory writes, MCP tool descriptions, and skill installs for promptware and wraps tool results in `<untrusted_tool_result>` with lookalike defanging; OpenHands wraps repository content in `<UNTRUSTED_CONTENT>`; sandboxes scrub provider credentials from the environment by name pattern (P §10.7, §10.3, §8.5). Tool usability is an engineering investment equal to UI: clear names, parameter descriptions and examples, errors that say what went wrong, tested (G §16, ACI).
- Operate: one skill, one thing; description reads like a one-line handoff; gate by paths or requires. Third-party skills get scanning and trust tiers. Do not wrap every API endpoint as a tool; consolidate (P Rec 17). Untrusted-content boundary: delimit tool and web results, scan context files and memory writes, keep credentials out of the agent's environment. Every tool ships with a description, a typed argument schema with validation at the boundary, parameter examples, actionable error text, and a test.

### A11. Edit-tool contract follows model tier and model dialect. Never line numbers. **[coding-specific]**
- Evidence: frontier models → exact unique-substring replacement (Claude Code; Mistral Vibe converged mid-2026). Weaker or open models → fuzzy cascade (OpenCode 9-stage, Hermes 9-strategy, Aider RelativeIndenter). LLM edit-repair is a third option (Gemini CLI). Dialect matters independently of strength: GPT-family models get a Codex-style patch DSL and lose string-replacement tools in OpenCode (P Obs 4, Rec 5).
- Operate: exact match for frontier models by default; a per-model table selects patch DSL where the model family prefers it; add a cascade only when serving weaker models. Drift is handled in the tool.

### A12. Model-harness co-design is live. On each model upgrade, remove model-compensating scaffold; expect platform surfaces to keep growing.
- Evidence: Codex re-tunes prompts server-side per model release; Anthropic deleted Sprint and context-reset after Opus 4.5/4.6 (G §05, §08). At the same time, Codex's tree nearly doubled in one quarter on memories, Guardian, marketplace plugins, voice (P §14.5). "Thinner" means fewer workarounds for model weaknesses, not a smaller runtime.
- Operate: on every model upgrade, run a subtraction pass over rules, middleware, and orchestration that exist to compensate for the previous model. Keep a dated inventory; separate structural from inventory claims; record what each new snapshot supersedes.

### A13. Good harness engineering starts with good engineering. Humans keep accountability.
- Evidence: Stripe Minions' 1,300 PRs/week rest on a decade of devboxes, Bazel caches, test coverage, blue-green rollback built for humans; Stripe does not let AI auto-merge anything (G §09). Hashimoto: "I'm not shipping code I don't understand" (G §07). TDD and Tidy First work as harnesses unchanged (G §11). OpenAI enforces dependency layering early because "the constraint itself is the source of speed" (G §06).
- Operate: tests, reproducible environment, fast feedback, small separable commits (structure vs behavior), and rollback are prerequisites, not follow-ups. No auto-merge. Every shipped change has a human who can explain it. Agents amplify whatever engineering culture exists.

---

## 2. Anatomy we design against

Seven subsystems plus two cross-cutting surfaces (P §2.3), mapped to G's five components (G §04: Instructions, Constraints, Feedback, Memory, Orchestration) with G §16's capability discussion noted where it applies. Every system we build takes an explicit position on each row, even if the position is "deliberately absent."

| P subsystem / surface | G component | Minimal viable form | Our default upgrade trigger |
|---|---|---|---|
| Agent loop | Orchestration | linear `while`, serial tool execution, step + cost + wall-clock caps | ≥3 orthogonal turn policies → middleware pipeline (P Rec 1) |
| LLM integration | — | one provider call | multi-provider → central per-model table covering cache breakpoints, thinking tiers, reasoning effort, edit dialect; re-validated against the task set on every model or provider release, since only vendors get server-side re-tuning (P Rec 2, Obs 2, §15.4) |
| Tools & actions | (G §16 capability) | bash + read + write + search_replace; each with description, typed argument schema, examples, actionable errors, test | tool count >~15 → deferred loading (P Rec 4) |
| Memory & context | Memory | context-file walk-up at project, user, extension scopes, reading neighbor filenames; nested files surfaced JIT on subtree touch; threshold compaction (P Rec 6–7) | cross-session need → documented write paths and read paths per A8 |
| Safety & permissions | Constraints | hard floor + PLAN/DEFAULT/YOLO approval modes + scope patterns (P Rec 9, 11) | shared or automated context → sandbox + policy-as-code + audit log (P Rec 10) |
| Orchestration | Orchestration | none | A9 case (c) → human-orchestrated parallel sessions; A9 cases (a), (b), (d) → in-process sub-agents under the containment contract |
| Extensibility | Instructions / (G §16 capability) | hooks + SKILL.md directory | external process → MCP; third-party → trust tiers (P Rec 14) |
| Interface layer | — | terminal | any non-human driver → ACP server (P Rec 13) |
| Session substrate | — | transcript persistence, resume | unattended runs → turn-level checkpoint with file restore (P Table 11) |
| Feedback | Feedback | tests the agent runs; pre-completion check | shipping work → independent reviewer; entropy → GC agent (G §06). In P this appears as the reflection loop (§6.3) and outer verification loop (Table 12), not as a named subsystem |

---

## 3. Decision rules

Thresholds are tunable defaults with their source stated. Inventory-dated 2026-11-07.

1. **Rule or constraint?** By consequence of violation: irreversible, catastrophic, **or costly to reverse** → hook / policy / CI / sandbox. Cheap to fix in review → instruction file. G §15's own example: "pushed to main? Revertable but painful — constraint."
2. **When to add or remove a rule.** Conventions and style: add only after an observed failure. Safety red lines and architectural invariants: seeded day one (G §05, §15). Removal has two classes. **Conventions** may be removed when (a) the current model passes the tuning set without the rule, or (b) the rule contradicts a newer rule and the newer one wins on the task set. Non-firing alone is not grounds; a rule that has never fired may be preventing the failure. **Safety red lines and architectural invariants** are exempt from (a) and (b): they are removed only by explicit human decision after the protected scenario has been re-verified as impossible or harmless (G §05 keep-list; P Rec 11's floor that survives bypass).
3. **Instruction file budget.** ~100 lines root, pointers to docs, subdirectory files for subsystems, router when workspaces diverge (G §14). Scopes: project, user, extension; read neighbor conventions (AGENTS.md, CLAUDE.md) (P Rec 6).
4. **Tools.** Start at four (our choice; P Rec 3 says one). Add one per observed failure mode. Deferred loading past ~15 (P Rec 4). Every tool: description, typed argument schema validated at the boundary, parameter examples, actionable errors, a test (G §16).
5. **Compaction defaults.** Fire when estimated tokens exceed `window − buffer`. Buffer default: `max(13K tokens, 10% of window)`; the 13K figure is Claude Code's (P Rec 7), the 10% floor is ours so the buffer scales with larger windows. Verbatim tail default: last 30% of messages (Gemini CLI's ratio, Listing 3's unit). Alternatives observed: Pi ~20K recent tokens, OpenCode the last two user turns (P §9.5). Tune per harness against the task set. Merge the previous summary rather than re-summarize. Route overflow errors into the same routine (reactive compaction).
6. **Stuck detection.** Ship the cheap caps: consecutive malformed responses (cap 3); wall-clock per run defaulting to 2× the task set's p95 successful duration; three identical tool calls → ask (P Rec 18). Headless runs define ask-handling explicitly: OpenCode auto-rejects all asks in headless mode (P §10.9); we default to abort-and-report, never auto-approve. A heavier detector (hash signatures, LLM stuck check) is adopted only if, on the tuning set, more than 5% of runs hit a cheap cap without having resolved the task.
7. **Multi-agent.** Only on A9 cases (a) exploration, (b) independent review, (c) independent modules in isolated worktrees, (d) green-field beyond model range. Case (c) uses human-orchestrated separate sessions, not sub-agents. Cases (a), (b), (d) use in-process sub-agents; children never gain tools or permissions the parent lacks. Summaries out, files for results (P Rec 12–13; §11.3, §11.7; G §17).
8. **Memory.** Document every write path as `{writer, reviewer, store, bound}` and every read path as `{what, when}`; "no write paths" is a valid, documented position. Static startup snapshot: character bound, frozen per session. Dynamic per-turn content: after the cache boundary, bounded per turn, refresh rule stated. On-demand durable store: unbounded, indexed. All writes scanned (P Obs 5, §7.2, §9.6, §10.7).
9. **Retrieval.** Code: ripgrep / glob / tree-sitter; embeddings only after a held-out win over that baseline (P Rec 16). Conversation and notes: FTS5/BM25 default; hybrid after a held-out win on our recall tasks.
10. **Reasoning budget.** Tested default for time- or cost-bounded tasks, exactly as measured in G §10: xhigh for planning, high for implementation, xhigh for verification. Phase boundaries are the plan-approved event and the pre-completion check. Untimed tasks: measure before adopting.
11. **Environment awareness.** Inject cwd structure, available binaries, and runtime versions at session start (LangChain LocalContextMiddleware, G §10). Reproducibility alone does not give the agent this.
12. **Interface.** Any harness driven by something other than a human at a terminal ships an ACP server (P Rec 13).
13. **Approval modes.** Every interactive harness has PLAN (read-only), DEFAULT (ask on consequential), and an explicit bypass mode with a floor beneath it (P Rec 9, 11).
14. **Accountability.** No auto-merge. A human who understands the change signs off on anything that ships (G §07, §09).
15. **Dating.** Every inventory claim carries a date and a source snapshot; a newer snapshot records what it supersedes (P §14.5).

---

## 4. The operating loop

Boosting, not redesign (G §10; G §07). One structural change per cycle, so the effect is attributable.

0. **Task set.** Fixed task distribution split three ways: a **tuning set** used for every cycle decision; a **held-out set** used only at phase checkpoints (end of each §7 phase) and refreshed after each use so it never drives selection; and a per-cycle **target metric**. Fixed model. Target success rate p and a tolerated regression band on success rate set up front (P §15.5's operational definition; G §10's overfit warning).
1. **Baseline.** Measure success rate, cost, wall-clock, and safety-incident count on the tuning set. Record for attribution; the baseline is not a gate.
2. **Trace.** Read failure traces. Classify: self-confirmation, doom loop, environment unfamiliarity, time/reasoning misallocation, context pollution, missing tool, missing constraint.
3. **One targeted structural fix.** One mistake → one engineered solution (rule, tool, hook, middleware). Hardest constraint the consequence justifies (Rule 1). Declare the cycle's target metric before running it: success rate, cost, wall-clock, safety incidents, or instruction-file line count.
4. **Regression.** Re-run the tuning set. Two acceptance routes. **Performance route**: the cycle's declared target metric improves **and** success rate stays within the regression band. **Compliance route**, for mandated safeguards (hard floor, approval modes, credential scrubbing, untrusted-content delimiting, containment contract): accept when a coverage probe passes (an adversarial test demonstrating the safeguard blocks its target scenario, for example a `rm -rf /` attempt under bypass mode is refused) **and** success rate stays within the band; no benchmark gain is required, since benchmarks do not measure these (P §15.5 "scaffolding that benchmarks don't measure"; P Rec 11). Operational cycles (cost, simplification) take the performance route on their own metric. **Both routes** also re-run the full set of existing safeguard coverage probes; any probe that previously passed and now fails rejects the cycle, whatever its target metric did (P Rec 11 "policies-as-code survive refactors"; G §10 regression testing). A human confirms the fix is a general mechanism, not a patch for a specific task.
5. **Checkpoint.** At each §7 phase end, run the held-out set once. If held-out success fell outside the band, bisect the phase's accepted cycles. Then refresh the held-out set.
6. **Subtract.** Periodic GC under Rule 2's two removal classes: contradictory conventions, conventions for old-model weaknesses, docs drift. Optionally a dedicated GC agent that only files cleanup PRs (G §06). Mandatory on every model upgrade (A12).

---

## 5. Do not build

- A general agentic framework in the runtime, absent a measured reason (P Rec 15; default, see A3 caveat).
- Vector RAG over code without a held-out win (P Rec 16).
- 1:1 API wrapper tools (P Rec 17).
- Line-number edit tools (P Rec 5).
- Heavy stuck detection before cheap caps are measured insufficient (P Rec 18).
- Multi-agent for parallel implementation of coupled code (P §15.1).
- Instruction files that restate what the code already says (G §05, §14).
- Rules "just in case", except safety red lines (G §05).
- Auto-merge (G §09).

---

## 6. Reference scaffold and its gaps

P §16.10, Listing 3: ~90-line Python minimum viable harness. Linear loop, Protocol-typed model, bash/read/write/search_replace, root-to-cwd AGENTS.md discovery, turn and cost caps, threshold compaction keeping the last 30% of messages. P states it implements 10 of 18 recommendations and is "illustrative scaffold, not production code."

What must be added before it satisfies this document:
- Hard floor and approval modes (A4; Rules 1, 13). Listing 3 has no permission layer.
- Pre-completion verification. Listing 3 returns the moment the model emits no tool call (line 78).
- Malformed-response cap and wall-clock cap (Rule 6). Listing 3 caps turns and cost only.
- Environment injection at start (Rule 11).
- Untrusted-content delimiting on tool output (A10).
- Tool descriptors with typed argument schemas and boundary validation, plus examples; Listing 3 emits only `name` and `description` and calls `**call["args"]` unvalidated.
- User- and extension-scope context files, and JIT injection of nested context files when a tool touches their subtree (P Rec 6); Listing 3 walks project ancestors once at start.
- Reactive compaction on provider overflow errors (P Rec 7); Listing 3 compacts on its own token estimate only.
- Session substrate: transcript persistence and resume (P §2.3); Listing 3 holds messages in memory only.

Copy and specialize; do not library-ize.

---

## 7. 90-day plan

One change per regression cycle (§4). The schedule below sets priority order, not batch boundaries. Each item is its own cycle with its own before/after measurement.

For non-coding harnesses (ops, research), the performance-route items below (tool set, compaction, deferred loading, retrieval, memory, environment injection) are hypotheses for that domain's task set; drop any whose cycle fails to show value there. The compliance-route items (hard floor, approval modes, costly-to-reverse rules as constraints, pre-completion check, untrusted-content boundary, credential scrubbing, human sign-off) are required in every domain and are removed only under Rule 2's safety-class procedure.

**Weeks 1–2: Instrument.**
- For each harness we run, fill the §2 anatomy table with its actual position. Date it.
- Build the task sets (tuning + held-out) per harness domain. Record baseline success rate, cost, wall-clock, safety incidents. Set p and the regression band.

**Weeks 3–6: Bottom line and verification, one cycle each.**
- Cycle: hard floor under every bypass path.
- Cycle: approval modes and scope patterns where missing.
- Cycle: move costly-to-reverse rules from prose into hooks/policy.
- Cycle: pre-completion check against acceptance criteria.
- Cycle: delete-test the root instruction file toward ~100 lines.

**Weeks 7–10: Context, memory, security, one cycle each.**
- Cycle: compaction thresholds and tail; verify overflow routing.
- Cycle: deferred loading where tools exceed ~15.
- Cycle: memory write and read paths documented per Rule 8; startup snapshot bounded and frozen; dynamic content given a refresh rule; durable store indexed; all writes scanned.
- Cycle: untrusted-content delimiting and credential scrubbing.
- Cycle: environment injection at session start.

**Weeks 11–13: Attribute, subtract, institutionalize.**
- Phase checkpoint on the held-out set (§4 step 5); each accepted cycle already has its own attribution.
- Subtraction pass against the current model under Rule 2.
- Fix the §4 loop as a cadence tied to model releases.

**Exit criterion.** Every harness has: a dated anatomy table; a tuning and held-out task set with **current** measured success at or above target p and the last held-out checkpoint within the band (the baseline is recorded for attribution only and does not gate exit); a hard floor and approval modes; a pre-completion verification gate plus a named human sign-off path; documented memory write and read paths; a sub-agent containment contract if sub-agents exist; and an untrusted-content boundary. Current measured success below p means the exit criterion is not met regardless of which mechanisms exist.
