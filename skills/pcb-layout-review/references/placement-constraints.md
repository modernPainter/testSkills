# Sourced Project Placement Conditions

Use for requirements from datasheets, mechanical drawings, enclosure CAD, component placement guides, or explicit user requests.

Each condition must identify its object, applicable revision, source location, datum/units, permitted degrees of freedom, and verification method. Experience-based distances without project evidence remain suggestions, not universal hard thresholds.

## Constrain only what the evidence supports

- Fully fixed pose: explicit position, rotation, side, and tolerance; current coordinates alone are insufficient.
- Local edge/orientation/assembly-side condition: constrain only the corresponding degrees of freedom, preserving other authorized changes.
- Distance and proximity: specify whether measurement uses body edges, component centers, or particular pads; keep them distinct.
- Keepouts and service space: specify region, direction, datum, and applicable side; distinguish two-dimensional projection from actual three-dimensional volume.
- Conversational fixed conditions: apply through every revision in this task without changing native Lock or creating another persistent fixed property.

## Evidence and conflicts

Confirmed clauses may establish project hard conditions. Ambiguous graphics, uncertain part/revision matches, missing units/datums, and conflicting requirements remain pending confirmation. Documentation supporting only a suggestion cannot enforce it as a hard rule.

The PCB supplies operational facts; the schematic cannot unconditionally override them. Ask when a conflict changes hard conditions or execution scope. For soft suggestions, disclose the conflict and continue independent work; keep the conflicting dimension unverified.

## Checking

Recompute each clause against the actual candidate and retain objects, measurements, and sources. Reading a requirement does not prove that placement capabilities can enforce it. Stop affected operations when a required condition cannot be guaranteed and report it as unmet.
