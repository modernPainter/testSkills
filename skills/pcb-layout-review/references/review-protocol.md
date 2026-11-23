# Independent Layout Review Protocol

Finish each read-only review with an explicit candidate and verdict. Review every complete candidate, including those that passed tool hard checks. Return issues and evidence-backed improvement opportunities to the layout workflow. Keep board edits outside review, and finish with a verdict rather than repeatedly rechecking the reviewer's own opinions.

## 1. Establish the object and scope

Obtain the task input, actual candidate/current board, native Lock and conversational fixed conditions, allowed poses, and applicable project documentation. Use the original project as an additional reference only when it exists and is relevant. Ordinary review does not require historical stripped or scattered boards.

Required facts cover identities, actual board shape/holes/component geometry, fixed conditions, applicable mechanical restrictions, and placement-caused electrical conflicts. Explicit project power, clock, or other placement requirements also make their evidence and checks required. Without such declarations, missing a complete datasheet collection does not block otherwise covered basic checks.

First reconcile designators, identities, counts, known poses, and board shape. If readings disagree with the actual board, report unreliable review data and stop deriving new geometry conclusions.

Derive front/back counts and side-change counts from the candidate's whole-board facts, not accumulated filtered pages or copied input counts. Record baseline and current sides separately. Before claiming components "remain on the back" or "preserve their side", reconcile baseline and candidate sides by designator and list the objects and both readings in `side_assertions`. Use current candidate readings in `pose_facts`. List unmatched identities and missing baselines separately; equal counts do not establish equivalence. This reconciliation adds no fixed conditions: permitted side changes still follow task authorization and engineering evidence.

With `pcb_read_board_evidence section=candidate`, begin with summary and obtain details needed for each finding. Follow actual pagination for any claimed complete detail acquisition, and record uncovered scope honestly. Revision does not require all pages. Whole-board host checks and the business verdict remain separate from public fact delivery.

### Candidate geometry delivery

These completeness rules apply when claiming complete raw component delivery or geometry reuse. They do not require full-board pagination before ordinary repair.

Complete raw `geometryFeatures` rows include `feature`, `feature_index`, `feature_count`, and the full component's `component_sha256`. Feature 0 also carries `component_metadata`, preserving other facts such as holes. A revised candidate's `geometry_reference` row uses `feature_index=null` for an entirely unchanged component and binds the direct parent and manifest, component hash, and the first completely delivered candidate and manifest.

An explicit reference means the host verified complete delivery of the parent component under the same task and owner, and exact equality of the child's identity, pose, side, fixed conditions, and all geometry. Reuse those delivered raw facts; continue reading all raw features of changed components. One reference row covers a whole component. Pagination `total` counts response rows, not the sum of their `feature_count` values.

Follow actual `next_cursor` values to completion. Match all component IDs against complete `poses` and `geometry_component_count`. Zero-feature components without references still require matching `geometry` rows. Missing pages, baseline geometry, planning bounds, or standalone hashes are not explicit references and cannot be credited as delivered facts. Report missing facts if the referenced original cannot be located.

Recheck whole-board hard conditions, findings, and quality costs for this candidate. References do not inherit a parent's qualification. Record model-turn/context limits separately from the shared task deadline. Complete remaining valid pagination before advancing; early termination is not task timeout.

## 2. Classify evidence

### Spatial evidence

For spatial or quality claims, read the relevant `spatialFacts`, `quality`, `qualityNets` and `qualityModules` facts. Each spatial row binds exact module members to baseline and actual candidate footprint-origin ranges, X/Y spans, and front/back counts. A missing candidate range is unknown, not zero. These measurements describe origins, not physical body extents or mechanical support.

For claims such as "distributed across the board", "near an edge", or "separated input and output regions", name the objects and report their actual candidate coordinate ranges. Consult their individual poses and real board outline as needed; module planning anchors and baseline positions cannot prove the claim. Reconcile the written conclusion against these numbers before submitting review. Preserve reduced spans and connection-distance increases as explicit observations and explain any engineering tradeoff; do not hide them behind a higher aggregate score.

Mounting holes clustered along one edge may be an evidence-backed engineering concern, but their category or original positions alone do not create mandatory mechanical datums. Without a sourced requirement, disclose the concern and missing mechanical verification rather than inventing a minimum separation or automatically restoring input poses.

- Direct observation: objects, graphics, annotations, and requirements in the actual board or project documents.
- Reproducible measurement: distances, overlaps, displacement, and issue changes computed from trustworthy facts.
- Sourced requirement: a matching component, revision, datum, clause, or explicit user requirement.
- Inference/risk: functional guesses, placement experience, and unconfirmed corridor suggestions.
- Unverified: missing data, unreliable readings, or checks not performed.

Net names do not prove electrical roles, positions do not prove anchors, and ambiguous graphics do not fix complete poses. Missing information does not imply a pass.

Input poses establish current state only; scattered inputs especially cannot define mandatory restored mechanical positions. `modules.anchors` are planning references, not mounting datums. Reconcile native Lock, task-specific fixed conditions, and sourced project clauses individually; each clause constrains only supported degrees of freedom. Mounting-hole categories, connector courtyards, and voltage labels may suggest optimization. Without applicable sources, list them as risks/unverified rather than asserting mandatory hole restoration, impossible wiring, or a universal clearance violation. Reproducible collisions and project hard requirements remain subject to hard checks.

Distinguish verified satisfied, confirmed failed, unverified, and justified not applicable for each item. Risk is a separate dimension, not a replacement for verification status. Record objects/pins, source locations, measurement endpoints and units, actual values, applicable requirements, and uncovered scope. Explain why applicability conditions fail before marking not applicable; missing information is not non-applicability.

## 3. Hard checks and issue comparison

Each retained candidate receives its own whole-board checks before comparison. Parent results cannot qualify a child. Read the current public candidate facts and use host assessment for the complete revision.

Reconcile identities, immutable nets/footprints and other facts, native fixed components, conversational fixed requirements, and protected non-pose objects individually. Check actual board shapes/holes, bodies/courtyards/full pads for out-of-board geometry, collisions, clearances, and applicable local restrictions. Origins and bounding boxes are proxy clues only.

Combine applicable placement DRC with item-level comparison against the input under identical rules: new, worsened, resolved, and pre-existing issues. Compare the original project separately when available. Distinguish expected unrouted connections from placement shorts/collisions; equal or reduced totals do not establish a pass.

Manual preview is independent of background engineering checks; complete all required checks before qualification or adoption. Fixed-pose-only violations may be explicitly accepted for unqualified adoption under the host contract. Assess engineering relationships and quality for each complete initial and revised candidate as well.

Actual hard checks govern acceptance. If a quality summary reports zero outside/overlap counts while real checks fail or lack coverage, report the actual issues and objects; do not claim board-edge compliance or collision freedom. Distinguish pre-existing fixed-component conflicts from new conflicts caused by candidate intrusion. The former still require resolution of contradictory fixed conditions rather than unauthorized unlocking.

## 4. Engineering relationships and tradeoffs

Check sourced functional groups, dedicated peripherals, interface direction, crowding, and measured corridors. Peripheral ownership cannot be reassigned solely to the nearest same-net component. Short geometric distance does not certify actual return paths, signal integrity, or thermal performance.

Apply the [conditional engineering review dimensions](../../pcb-layout-engineering/references/upstream-principles.md) to functional partitioning, power/decoupling, switching/feedback, interfaces/protection, signal space, and edge/mounting conditions. Clean hard checks and DRC do not automatically establish sound engineering relationships. General experience and other review opinions remain clues until their objects and evidence are verified; they do not create universal thresholds.

For each finding, record severity, objects, location/measurement, source, actual impact, and the smallest recommendation. Separate doubts from established defects, and list correctly satisfied critical conditions with evidence. Compare candidates under identical conditions and criteria; overall scores do not replace engineering requirements.

## 5. Verdict and handoff

| Verdict                            | Condition                                                                                                  |
| ---------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| Failed                             | Reproducible placement hard errors, or the actual board disagrees with the selected layout/immutable facts |
| Incomplete                         | Missing required documentation/checks, or the review target cannot be established                          |
| Pending review                     | A major object-specific, evidence-backed engineering concern remains unresolved by retesting               |
| Pre-route layout acceptance passed | All required conditions verified, no placement hard errors, and no major unresolved objections             |
| Improved this round                | Measured progress over the input; does not automatically change the verdicts above                         |

Return the explicit candidate, verdict, issue objects and evidence, verified conditions, unverified items, major objections, and actionable evidence-backed improvements to the layout workflow. List credible improvement opportunities even for a qualified current candidate. If none exist, state that rather than inventing requirements to force more rounds.

Classify unverified items as required for this stage, later-stage, or optional risk checks, with the missing documents/capabilities. Required unverified items make review incomplete. Keep later-stage/optional gaps disclosed without claiming a pass or demanding all possible documentation for basic checks that already have sufficient coverage.

Record accepted risks with objects, reasons, and confirmer; they cannot waive hard errors or required gaps. Review the whole new candidate after the next revision, not only moved components. Reopening the file is not an additional user requirement.
