## Section A

- [ADDRESSED] Round-2 A, A2 — Revision 3 L34–35 explicitly qualifies structural portability as “suggestive, one ablation” and preserves prompt restructuring’s contribution.
- [ADDRESSED] Round-2 A, anatomy “LLM integration” — L92 requires revalidation of provider mappings on every model or provider release.
- [ADDRESSED] Round-2 A, A10/A11 and anatomy “Tools & actions” — L69 and L111 require typed argument schemas and boundary validation.
- [PARTIAL] Round-2 A, §2 memory trigger versus Rule 8 and exit criterion — L61 and L115 permit coexistence and absence, but still impose character bounds and frozen snapshots on all file stores.
- [ADDRESSED] Round-2 A, A7 and Rule 5 — L112 specifies `max(13K tokens, 10% of window)` and distinguishes the sourced value from the chosen scaling rule.
- [ADDRESSED] Round-2 A, Rules 2 and 6 — L109 and L113 define removal conditions, runtime-budget sizing, and a quantified prerequisite for heavier detection.
- [ADDRESSED] Round-2 A, §4 baseline/regression and §7 exit criterion — L130–135 reserve held-out evaluation for checkpoints and refresh after use, while L202 requires current target performance.
- [ADDRESSED] Round-2 A, §7 scope and mandatory migrations — L22 explicitly makes every default, including tools, orchestration, and §7 requirements, provisional outside coding.
- [PARTIAL] Round-2 A, A6/A9 and Rule 7 — L64–65 add independent implementation and remove evaluator-only causation, but L96 and L114 still force the manual-session case into in-process delegation.
- [ADDRESSED] Round-2 B, Rule 2 and §4 subtraction — L109 exempts safety red lines and architectural invariants from ordinary deletion tests, and L136 incorporates that exemption.
- [PARTIAL] Round-2 B, §4 regression and §7 cycles — L133–134 permit operational metrics, but strict improvement still rejects required safeguards when measured incidents are already zero and other metrics remain unchanged.
- [ADDRESSED] Round-2 B, §7 exit criterion — L202 explicitly removes baseline performance from the exit gate.
- [ADDRESSED] Round-2 B, A9 causal attribution — L64 expressly states that the comparison cannot isolate the evaluator’s contribution.
- [ADDRESSED] Round-2 B, Rule 10 — L117 restores the measured xhigh → high → xhigh configuration.
- [ADDRESSED] Round-2 B, A9 heading/count mismatch — L63 removes the numerical claim and L65/L114 consistently enumerate four cases.
- [ADDRESSED] Round-2 C, evaluation leakage — L130–135 replace repeated per-cycle held-out selection with checkpoint evaluation and subsequent refresh.
- [ADDRESSED] Round-2 C, memory authority — L61 and L115 document each write path’s writer/reviewer separately from read paths and explicitly allow coexistence.
- [ADDRESSED] Round-2 C, scaffold integration checklist — L164–167 add schemas/validation, nested-context discovery, reactive overflow handling, and persistence/resume.
- [ADDRESSED] Round-2 C, delegation containment — L65 and L114 explicitly require inherited sandbox/policy and prohibit additional child tools or permissions.

## Section B

- [SEVERITY: high] FOUNDATIONS.md L177, versus L109 and L184–195 — “Drop any item whose cycle fails to show value” allows non-coding harnesses to discard hard floors, approvals, or credential protection without Rule 2’s human decision and impossibility/harmlessness check; domain uncertainty needs an explicit exception for required safeguards — P Recommendation 11, L3740–3742: “Regardless of tier, codify safety rules as data or dedicated policy files … if you support a YOLO mode, keep a floor beneath it”; G §05, L744–750 places “Critical safety and quality red lines” under “Rules to Keep.”

- [SEVERITY: medium] FOUNDATIONS.md L65, L96, L114 — The newly admitted human-orchestrated independent-module case is converted by the anatomy table and Rule 7 into mandatory in-process sub-agents; distinguish separate human-driven sessions from children inside one runtime — G §17, L2361–2366: “Launch a Claude Code session per worktree” and “You switch between sessions, review results, resolve conflicts, merge code”; P Recommendation 13, L3768–3769 scopes its restriction to “main-agent ↔ sub-agent coordination within your own runtime.”

## Section C

- [SEVERITY: medium] FOUNDATIONS.md L61, L115, L193 — Memory policy still applies a prompt-cache technique according to storage medium: file storage alone does not justify bounding the durable corpus or freezing every retrieval view; scope character limits and freezing to injected snapshots, with separate policies for durable files and on-demand reads — P §9.6, L1847–1849 describes Hermes’s bounded files “injected as a frozen snapshot, so mid-session writes hit disk without invalidating the prompt cache”; L1831–1836 separately describes Codex’s filesystem artifacts and “citation tracking and usage-based ranking.”

- [SEVERITY: medium] FOUNDATIONS.md L133–134, L184–195 — The acceptance rule has no compliance-based route for mandatory hardening: adding a required floor can leave already-zero observed incidents, success, cost, duration, and instruction length unchanged, so none of the permitted target metrics improves; require demonstrated safeguard coverage rather than an incidental benchmark gain — P §15.5, L3580–3581: “Safety requirements: Enterprise deployments demand scaffolding that benchmarks don’t measure”; P Recommendation 11, L3744–3745: “its twelve hardline patterns survive –yolo.”

VERDICT: REVISE — Most round-2 findings are closed, but safeguard acceptance and removal remain inconsistent, and memory and orchestration policies still need narrower applicability.
