# Pre-Route Routing-Space Review

After geometric safety checks, assess available escape/corridor risks without claiming completed routing.

## Required information

Use actual board shape, holes/keepouts, footprint bodies, courtyards, pads, and applicable project track-width/clearance/via requirements. Untraceable rules and incomplete geometry mean insufficient coverage; do not fill in defaults.

## Review objects

- Whether neighboring components block critical pin escape and whether actual pad orientations support expected connections.
- Observable corridors between strongly connected functional blocks, continuous component walls, and connections forced through narrow gaps.
- Fine-pitch escape space measured against real pads and project requirements; a package name alone does not establish fanout analysis.
- Corridors clipped to actual board shape, including holes, slots, and keepouts; the outline's enclosing rectangle overestimates usable space.

## Conclusion

Record objects, locations, measurements, rule sources, and coverage limits. Violating a declared hard escape requirement fails that condition. Without quantitative checks, report crowding risk only; unmeasured does not mean safe.

Connection-length, crossing, and crowding metrics support same-condition trend comparisons. They do not replace corridor facts or establish inter-layer paths, return continuity, or a complete routing solution. Actual routing, signal integrity, thermal performance, and manufacturing remain unverified.
