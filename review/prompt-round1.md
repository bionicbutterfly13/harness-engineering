You are reviewing a foundations document against its two source texts. Read all three files in full before writing anything:

1. review/paper-2609.00006.txt  — Barbaste et al., "Harness Engineering: Anatomy, Architecture, and Evolution of Coding Agents" (arXiv 2609.00006, July 2026). Cited in the document as "P".
2. review/guide-v260411.txt — Huashu, "Harness Engineering: The Complete Guide" (April 2026). Cited as "G".
3. FOUNDATIONS.md — the document under review. It proposes first-principles axioms, an anatomy table, decision thresholds, an operating loop, anti-patterns, and a 90-day plan for how one practitioner should build harnesses and AI-based software.

Your job: validate and critique. Specifically:

A. FIDELITY. For every axiom (A1–A13), every row of the anatomy table, and every numbered decision rule, check the cited section numbers and the quoted numbers/claims against the source texts. Report each misattribution, wrong number, overstatement, or claim the sources do not actually support. Quote the source line that contradicts or fails to support it.

B. OMISSIONS. What do the sources say that a first-principles foundations document should include but this one does not? Prioritize: things the paper's 18 recommendations or 13 observations say that are missing, and things the guide's case studies say that are missing.

C. INTERNAL CONSISTENCY. Contradictions between axioms, between axioms and the decision rules, or between the plan and the axioms.

D. OPERATIONAL WEAKNESS. Where is a rule too vague to act on, or a threshold unjustified by the sources?

E. The sources disagree with each other in places (e.g., the guide's Anthropic three-agent architecture and $9 vs $200 example vs. the paper's single-agent-by-default stance). Identify disagreements and say whether the document resolves them correctly.

Output format, strictly:
- A section per letter A–E.
- Each finding as one bullet: `[SEVERITY: high|medium|low] <location in FOUNDATIONS.md> — <finding> — <source evidence with file and approximate line or section>`.
- End with a line `VERDICT: ACCEPT | REVISE | REJECT` and one sentence of rationale.
Do not rewrite the document. Do not pad. Do not modify any files.
