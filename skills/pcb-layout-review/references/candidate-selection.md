# Candidate Comparison and Selection

Use for multiple candidates in one task or before/after placement revisions. Perform independent checks before comparing quality. The reviewer supplies evidence and recommendations without editing the board.

## Common conditions

Verify identical inputs, project rules, fixed components, movable scope, permitted sides, documentation, functional groups, and peripheral-ownership evidence. Mark dimensions that cannot be fairly compared when conditions differ. Preserve original candidates and evaluation criteria rather than altering them to make a new candidate win.

## Classify before recommending adoption

1. Identity changes prevent application. Classify actual out-of-board geometry, collisions, electrical conflicts and sourced hard requirement violations as failed; keep diagnostic preview and repair available. Fixed-pose-only violations may be explicitly accepted for unqualified adoption under the host contract.
2. Mark missing required evidence or checks incomplete; missing measurements cannot count as an advantage.
3. Among qualified candidates, prioritize declared engineering requirements before soft metrics. Failure of every candidate does not relax qualification.

## Multidimensional tradeoffs

List hard issues, engineering relationships, unverified items, functional proximity, space/measured corridors, quality components, and available timings separately. Classify measured issues as new, worsened, resolved, or pre-existing. Different evidence coverage alone does not establish actual degradation.

Read summaries and relevant `poses`, `findings`, `geometry`, `spatialFacts`, `quality`, `qualityNets`, and `qualityModules` facts for two explicit candidates with `pcb_read_board_evidence section=candidate` and compare the returned facts. A missing finding does not automatically prove resolution, and an unchanged record does not prove unchanged severity; verify corresponding objects and native measurements. List newly unverified concerns even when hard-error counts fall. Different geometry conditions, rules, or identities prevent claims of improvement from these differences.

Before comparison, obtain the facts supporting its conclusions for the child and direct parent. Follow actual pagination when claiming a complete section. Completed comparison establishes fact acquisition, not acceptance. Insufficient coverage and findings without component identity remain unverified rather than proving pose infeasibility.

With `pcb_read_board_evidence`/`pcb_apply_initial_candidate`, inspect the direct parent's and child's relevant `quality`, `qualityNets`, and `qualityModules` measurements. Compare old values, new values, differences, and measurement coverage per affected net/module using actual pose changes, pad connectivity, and fixed module membership. List unmoved neighbors on those nets. Missing measurements are neither zero regression nor improvement. During selection, record every measured regression in `comparison_json.acknowledgedRegressionIds`, using `net:<net-name>` or `module:<module-key>`; explain business tradeoffs in `regressions` and the selection rationale. The host retains objective differences. Aggregate metrics or tidiness cannot conceal neighbor costs. Evidence-backed soft regressions may be accepted without relaxing hard conditions.

When the parent has hard errors and the revision is the first qualified candidate, still reconcile with the direct source; the failed parent need not qualify. For `pcb_initial_candidate action=review`, record those regressions in `review_json.acknowledgedRegressionIds` and explain the business tradeoff in the rationale/findings. Resolving hard errors does not erase soft costs. Hard-error removal and placement-quality improvement are separate conclusions.

A new candidate may be recommended despite a lower overall score if evidence shows improvement in a required engineering relationship. Explain improvements, regressions, and reasons rather than appearance alone. Equally qualified candidates with different strengths may coexist; do not invent a unique optimum. Unsupported grouping or metrics cannot count as verified.

An overall score without metric components or engineering tradeoff evidence supports only a tentative ranking. Obtain the necessary components before recommending selection, or state insufficient evidence. Do not invent improved objects or assert that the higher score identifies the best layout.

Selection belongs to the layout workflow. Retain existing qualified candidates rather than replacing them with the latest trial. Diagnostic trials may be soft-previewed; their failures remain visible and adoption is decided separately. Recheck the entire board after local revisions, and distinguish optimization stopping from layout failure when a qualified result exists.

Record revision limits, time limits, lack of improvement, and capability gaps independently. One failed candidate does not prove there are no other feasible improvements, and the reviewer must not invent infeasibility or stopping grounds for the workflow.

Inspect current complete-candidate checks, especially new or worsened issues affecting stationary neighbours. Read pose_authorization under actual fixed conditions and allowed sides. Missing measurements remain unverified; retain the task attempt record and stopping state without inventing extra authorization requirements.
