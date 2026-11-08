## Section A

- [ADDRESSED] A1 — Revision 2 L30–32 replaces the universal performance claim with “has not been shown” and explicitly preserves the matched-condition uncertainty.
- [ADDRESSED] A1; anatomy “Tools & actions”; decision rule 4 — L32 and L111 distinguish P’s bash-only recommendation from the document’s chosen four-tool default.
- [PARTIAL] A2 — L35 acknowledges prompt restructuring and quotes “suggestive evidence,” but L34 still states greater structural portability categorically.
- [ADDRESSED] A3 and A8 — L39 explicitly identifies OpenClaw’s default hybrid retrieval and limits Hermes’s lexical-search claim to Hermes.
- [ADDRESSED] A3; decision rule 9; §5 vector-RAG prohibition — L41, L116, and L142 restore the held-out exception for code retrieval and separately state the chosen conversation-recall policy.
- [ADDRESSED] A6 — L51–53 distinguishes unsupported self-assessment from tool-grounded verification and removes the unsupported universal ranking.
- [ADDRESSED] A7; §7 weeks 7–10 — L56–57 and L187 correctly separate skill-loading adoption from the approximately 15-tool threshold.
- [ADDRESSED] A9 — L64 restores the qualification covering cross-process coordination without standard protocols.
- [ADDRESSED] A9 — L63–65 removes the unsupported “cheapest” claim.
- [ADDRESSED] A11 — L72–73 explicitly accommodates model-family patch dialects independently of model strength.
- [ADDRESSED] §2 anatomy introduction; “Tools & actions” and “Extensibility” rows — L87–97 names G’s five components correctly and labels capability as a separate §16 discussion.
- [ADDRESSED] §2 “Feedback (G only)” row — L100 explicitly locates feedback in P’s reflection and outer-verification loops.
- [ADDRESSED] Decision rule 1 — L108 includes costly-to-reverse actions and supplies G’s push-to-main example.
- [ADDRESSED] A9 citation — L64 now cites G §05 and §17 for Sprint removal and reduced evaluation frequency.
- [ADDRESSED] A4; anatomy “Safety & permissions”; §7 — L44–45, L95, and L180 specify approval modes, permission scopes, and deployment-dependent containment.
- [ADDRESSED] A6; §4 operating loop — L53 requires a plan and agreed, testable success criteria before implementation.
- [ADDRESSED] A6, A13; §7 exit criterion — L53, L81, L121, and L197 require human understanding, sign-off, and no automatic merge.
- [ADDRESSED] A4, A8, A10; anatomy safety row — L61 and L68–69 add untrusted-content handling, memory-write scanning, and credential protection.
- [PARTIAL] Anatomy “LLM integration” row — L92 specifies the required capabilities, but does not establish maintenance or validation of provider mappings across releases.
- [ADDRESSED] Anatomy “Memory & context” row; decision rule 3 — L94 and L110 include user/extension scopes, neighboring filenames, and just-in-time subtree discovery.
- [PARTIAL] A10, A11; anatomy “Tools & actions” row — L69 and L111 require examples, errors, and tests, but omit explicit argument schemas and validation.
- [ADDRESSED] §4 trace step; §7 plan — L118 and L190 require startup discovery of directories, binaries, and runtime versions.
- [ADDRESSED] A1; §6 reference scaffold — L20 introduces the single-call/fixed-workflow gate before selecting an agent loop.
- [ADDRESSED] Anatomy; A13 operating policy; §7 — L99 specifies persistence, resume, and checkpoint/file restoration, while L81 makes rollback a prerequisite.
- [ADDRESSED] A5 and decision rule 2 versus A4 and A13 — L49 and L109 explicitly permit day-one safety and architectural requirements.
- [ADDRESSED] A6 and A9 operating instructions versus A9 heading and decision rule 7 — L63–65 and L114 explicitly permit independent review.
- [PARTIAL] §2 memory upgrade trigger versus decision rule 8 and §7 exit criterion — L61, L115, and L197 permit no memory, but still impose one selection and uniform character-bound/frozen-snapshot rules across differing designs.
- [ADDRESSED] §7 weeks 3–13 versus §4 regression loop — L172 explicitly makes every scheduled item its own measured regression cycle.
- [ADDRESSED] §6 reference scaffold versus A4, A6, and decision rule 6 — L155–160 identifies the illustrative status and missing permission, verification, malformed-response, and runtime controls.
- [PARTIAL] A7 and decision rule 5 — L112 defines message-count retention and identifies alternative implementations, but never selects a buffer default or a method for sizing it.
- [PARTIAL] Decision rules 2 and 6 — L109 and L113 add deletion conditions, a malformed-response cap, and headless handling, but leave runtime-budget selection and heavier-detector acceptance criteria undefined.
- [PARTIAL] §4 baseline/regression steps; §7 exit criterion — L130–134 and L197 add targets and regression bands, but repeatedly use the same held-out set for change selection without protecting a final generalization check.
- [ADDRESSED] Decision rule 10 — L117 limits the schedule to bounded tasks and identifies planning and verification transitions.
- [PARTIAL] §7 scope and mandatory migrations — L22 qualifies only marked axioms, while A1’s tool default, A9’s orchestration restrictions, and §7’s universal requirements remain unqualified across domains.
- [PARTIAL] A6, A9, decision rule 7 — L64–65 adds review and full-stack cases, but omits G’s independent-implementation case and still attributes the comparison’s improvement specifically to the evaluator.
- [ADDRESSED] A3 versus A2 — L40–41 acknowledges LangGraph-based Deep Agents and makes hand-written loops an overridable default.
- [ADDRESSED] A12 — L75–77 separates removal of model workarounds from growth of platform infrastructure.
- [ADDRESSED] A4, A8, A12 — L8 explicitly identifies the superseded Codex hooks and memory claims.

## Section B

- [SEVERITY: high] Decision rule 2, L109; §4 subtraction, L135 — The new deletion rule permits removing any rule solely because it has not fired or contradicts a newer rule, without exempting safety requirements or architectural invariants; successful prevention is not evidence that protection is unnecessary — G §05, lines 745–751 explicitly retains “Project-specific architectural decisions and conventions” and “Critical safety and quality red lines”; P Recommendation 11, lines 3740–3745 requires a floor that survives bypass mode.

- [SEVERITY: high] §4 regression, L134; §7, L172–190 — Requiring every accepted cycle to improve tuning-set success rejects safety, recovery, cost, and simplification changes that preserve correctness, contradicting the document’s mandatory improvement program — P §15.5, lines 3553–3555 describes a plateau where “further scaffold work pays off in operational concerns (safety, UX, extensibility) rather than in completion rate.”

- [SEVERITY: medium] §7 exit criterion, L197 — Requiring “baseline and current measurements at or above target p” makes an initially underperforming harness permanently unable to exit, even after its current performance exceeds the target — P §15.5, lines 3571–3573 defines viability through the scaffold’s “empirical success rate on T with M” exceeding p, without requiring an earlier baseline to qualify.

- [SEVERITY: medium] A9 evidence, L64 — “It shows that the evaluator made a broken result work” still makes a causal attribution that the comparison cannot isolate; architecture, elapsed time, and expenditure changed together — G §08, lines 1023–1025 compares “Single Agent — 20 min — $9” with “Three-Agent Collaboration — 6 hours — $200,” without an evaluator-only ablation.

- [SEVERITY: medium] Decision rule 10, L117 — The claimed “tested default” says high reasoning at planning/verification and lower during implementation, which changes—or ambiguously renames—the measured configuration — G §10, lines 1308–1309 specifies “xhigh at the start for planning,” “high in the middle for implementation,” and “xhigh at the end for verification.”

- [SEVERITY: low] A9 heading versus operating rule, L63–65 — The heading permits “two named cases,” while the operating rule and Decision Rule 7 permit three — G §17, lines 2471–2473 separately identifies writer/reviewer sessions and full-stack Planner/Generator/Evaluator; P Recommendation 12, lines 3751–3752 supplies the exploration case.

## Section C

- [SEVERITY: medium] §4 task-set and regression policy, L130–134 — Repeated acceptance decisions on the same “held-out” set make it part of adaptive selection; the document needs an explicit policy for evaluation leakage and an untouched or refreshed release assessment — G §10, lines 1322–1326 requires “Human verification to prevent overfitting to specific tasks causing regression elsewhere”; the present procedure does not establish that protection.

- [SEVERITY: medium] A8 and Decision Rule 8, L60–61 and L115 — Memory selection still does not reliably answer who may write and who must review each path: the categories can coexist, and pre-turn recall describes reading rather than write authority — P §9.6, lines 1840–1846 describes Gemini using both “the model editing GEMINI.md” directly and extraction patches “which the user reviews and applies via /memory”; Hermes’s bounded, frozen files are a separate implementation at lines 1847–1849.

- [SEVERITY: medium] §6 scaffold integration checklist, L157–164 — The checklist remains insufficient to make Listing 3 satisfy the document: it omits argument schemas/validation, persistence/resume, nested-context injection, and reactive overflow handling — P Listing 3, lines 3909–3910 constructs tool descriptors with only “name” and “description”; P §2.3, lines 262–264 names “transcripts, persistence, resume/fork”; Recommendations 6–7, lines 3699 and 3707 require nested context and compaction “reactively on overflow.”

- [SEVERITY: medium] A9 and §2 orchestration, L65 and L96 — Delegation has no explicit containment contract preventing children from gaining permissions or tools unavailable to their parent; an in-process requirement does not establish that boundary — P §11.3, lines 2337–2339 says spawning “inherits the parent’s sandbox and execution policy”; P §11.7, lines 2422–2425 requires “subagent must not gain tools the parent lacks.”

VERDICT: REVISE
Revision 2 resolves most attribution errors, but its deletion and acceptance rules can undermine mandatory safeguards, and several operational contracts remain incomplete.
