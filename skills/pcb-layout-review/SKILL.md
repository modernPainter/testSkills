---
name: pcb-layout-review
description: Independently check pre-route PCB placement against its task input, fixed conditions, real geometry, and sourced engineering requirements. Use to assess a candidate, compare placements, or investigate a placement regression; review is read-only.
---

# PCB Layout Engineering Review

Independently assess whether a candidate or the current layout satisfies this task's engineering conditions. Generation success, scores, and screenshots are not review conclusions. Recompute from the actual board and applicable documentation; report issues, verified conditions, unverified items, and an explicit verdict. Review remains read-only.

Independent means a separate read-only review step performed by the same business Agent in its reviewer role; another Agent is not required. Identify the candidate and item-level evidence, and disclose which summary and detailed facts were examined. The layout workflow still decides whether to select and preview a qualified candidate; review does not adopt it for the user.

## Review sequence

Read the [review protocol](references/review-protocol.md) before starting to establish scope and completion criteria. Obtain this task's input, actual candidate/current board, fixed conditions, and applicable evidence. Ordinary placement review does not require additional historical sample boards.

Reconcile object identities, board shape, and known poses first. Then check actual geometry, fixed conditions, and applicable mechanical and placement-related electrical requirements. Review every complete initial and revised candidate delivered by the layout loop, including candidates that already passed tool hard checks. Combine placement DRC, quality assessment, and engineering requirements; aggregate error counts or one overall score are insufficient.

For multiple candidates or revisions, follow [candidate comparison](references/candidate-selection.md) item by item. Record each issue's object, location/measurement, evidence, impact, and recommendation. List pre-existing non-placement issues and expected unconnected items separately rather than claiming they were resolved. Return the explicit candidate, verdict, verified conditions, unverified items, and evidence-backed improvements to the layout workflow. The reviewer neither edits the board nor confirms adoption.

When the workflow uses `pcb_read_board_evidence`, read the retained candidate summary first. Inspect poses, findings, transformed geometry, spatial facts and quality details relevant to each engineering conclusion, including changed objects and stationary neighbours. Read relevant detail pagination according to each engineering conclusion; host checks cover the whole retained candidate. Whole-board host checks remain required for qualification. Record the actual public evidence scope; a summary does not establish delivery or understanding of all raw geometry. Partial candidates can be reviewed as incomplete and repaired without restarting.

## Verdict

Use actual candidate spatial and quality measurements to support distribution and tradeoff claims; planning anchors are intentions. Obtain the necessary details on demand. Missing required evidence prevents a pass or adoption, while preview and further repair remain available.

Reproducible placement hard errors mean failed. Missing required evidence or checks mean incomplete. Major object-specific, evidence-backed engineering disagreements mean pending review. Use "pre-route layout acceptance passed" only when all required conditions are verified, no placement hard errors remain, and no major unresolved objections remain.

"Improved this round" may coexist with failed or incomplete. Unsupported opinions remain risks. Record accepted risks with their objects, reasons, and confirmer. A moved native-locked or task-fixed footprint remains a fixed-condition violation: report its reference and original/proposed pose, and use `code: "FIXED_POSE_CHANGED"` on that finding when submitting the business review. The host permits explicit human adoption with these violations after the remaining checks and review finish; preserve the failed engineering verdict and unqualified adoption status. Other hard errors and required gaps remain adoption blockers. Actual routing, signal integrity, thermal performance, and manufacturing remain unverified.

## Read as needed

- [Quality metrics](references/result-metrics.md): metric components, displacement, crowding, and assessment coverage.
- [Project constraints](references/placement-constraints.md): sources, datums, permitted degrees of freedom, and conflicts.
- [Evidence extraction](references/evidence-extraction.md): available datasheets, mechanical drawings, or project annotations.
- [Electrical relationships](references/electrical-topology-evidence.md): evidence for drive direction, peripheral ownership, or power stages.
- [Routing-space review](references/routing-space-review.md): actual rules and geometry supporting escape/corridor assessment.
