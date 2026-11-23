# Extracting Project Evidence

Use for component documentation, mechanical/connector drawings, reference layouts, enclosure CAD, or explicit project annotations.

## 1. Match actual components

Establish designators, part numbers, footprints, and document revisions first. Component values or family names do not automatically identify exact parts. Preserve uncertainty rather than applying another family member's dimensions.

## 2. Locate the requirement

Record document title/revision, page/section/figure/dimension label/CAD feature, or this task's user requirement. Identify the constrained object, datum, direction, units, and degrees of freedom.

Distinguish confirmed requirements, conditional suggestions, and unresolved items. A same-name PDF, presentation graphic, or ambiguous sentence is not a hard rule. An experience-based preference for closer placement cannot become a distance absent from the source.

## 3. Check datums and dimensions

Enclosure origins, board edges, component bodies, and hole centers are different datums. When their relationship to the current PCB is unknown, avoid guessing coordinates or fixing a complete pose.

Board graphics and dimension annotations may serve as documentation when they clearly match objects and actual board shape. File extensions alone do not establish mechanical intent. Confirm conflicts affecting scope or hard conditions; disclose gaps affecting only soft suggestions.

## 4. Check applicability and coverage

Assess supported positions/orientations/sides, local distances, planar keepouts, and mating/tool corridors using the actual geometry. Two-dimensional placement alone cannot prove height, bent-cable clearance, stacked Z space, screw length, or thermal airflow.

After establishing a condition, check the candidate using [project constraints](placement-constraints.md). Unsupported checks and missing data remain unverified; acquiring documentation alone does not establish a pass.
