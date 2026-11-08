## Section A

- [PARTIAL] Round-3 A, “Round-2 A, §2 memory trigger versus Rule 8 and exit criterion” — Revision 4 L61/L115 distinguish read paths, but L193 still requires bounding and freezing whenever storage is file-based.
- [ADDRESSED] Round-3 A, “Round-2 A, A6/A9 and Rule 7” — L65/L96/L114 consistently assign independent-module implementation to human-orchestrated separate sessions.
- [ADDRESSED] Round-3 A, “Round-2 B, §4 regression and §7 cycles” — L134 permits mandated safeguards through demonstrated coverage and success-rate non-regression without requiring a benchmark gain.
- [ADDRESSED] Round-3 B, L177 versus L109/L184–195 — L177 explicitly retains required safeguards across domains and subjects removal to Rule 2’s safety-class procedure.
- [ADDRESSED] Round-3 B, L65/L96/L114 — L65 separates human-driven sessions from runtime children, and L96/L114 preserve that distinction for independent modules.
- [PARTIAL] Round-3 C, L61/L115/L193 — L61/L115 replace the storage-medium distinction with a read-path distinction, but still prescribe freezing all prompt-injected memory while L193 retains the original file-based requirement.
- [ADDRESSED] Round-3 C, L133–134/L184–195 — L134 adds the requested compliance route based on a passing safeguard probe rather than incidental performance improvement.

## Section B

- [SEVERITY: medium] FOUNDATIONS.md L61/L115 — The new requirement that “anything injected into the prompt” be frozen per session extends Hermes’s cache technique to dynamic memory and pre-turn recall; freezing must apply to the stable startup snapshot, with separate refresh rules for dynamically retrieved content — P §7.2, L1117–1118: “(session guidance, memory, environment, MCP instructions, language, scratchpad) are dynamic and computed per-turn”; P §9.6, L1852–1853 describes a memory sub-agent running “immediately before the main reply.”

## Section C

- [SEVERITY: medium] FOUNDATIONS.md L193 — “Bound, freeze, scan if file-based” still makes storage medium determine retention and cache policy, contradicting the revised A8/Rule 8 and potentially capping durable files intended for on-demand retrieval; the scheduled cycle must distinguish durable storage from injected snapshots — P §9.6, L1847–1849 describes Hermes’s particular bounded files “injected as a frozen snapshot, so mid-session writes hit disk without invalidating the prompt cache”; L1831–1836 separately describes Codex’s filesystem artifacts and injection with “citation tracking and usage-based ranking.”

- [SEVERITY: medium] FOUNDATIONS.md L134–135 — The performance route checks only its target metric and task-success band, while safeguard coverage is checked only on the compliance route; a later optimization can therefore pass the stated acceptance gates while breaking an existing mandatory safeguard, so applicable safeguard regression checks must gate both routes — P Recommendation 11, L3744–3746: “its twelve hardline patterns survive –yolo” and “Policies-as-code survive refactors”; G §10, L1322–1326 requires “Regression Testing” to prevent “regression elsewhere.”

VERDICT: REVISE — The principal safeguard and orchestration closures are sound, but memory-policy contradictions and incomplete safeguard regression gates leave material operational gaps.
