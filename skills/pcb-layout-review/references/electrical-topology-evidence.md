# Evidence for Electrical Relationships and Peripheral Ownership

Use when placement needs drive direction, decoupling/feedback ownership, or power-stage membership. Prefer schematics, datasheets, design guides, or explicit project documentation mapped to components and pins, and reconcile actual PCB connectivity.

## What PCB connectivity establishes

Shared-net endpoints establish connection facts, not driver/receiver direction, series-resistor function, internal switching conduction, or complete current loops. Names such as CLK, SW, FB, and DP/DM help locate documentation but do not establish confirmed electrical roles.

## Dedicated relationships

For decoupling, establish the served device and supply/return endpoints. For feedback, establish sense points, divider terminals, and the return endpoint. For source series resistance, establish the driver and receiver sides of the resistor. Confirm crystal loads and RF matching through their specific documentation.

Preserve relationship sources throughout the task. Closer placement cannot reassign peripherals to another same-net core. Resolve documentation/connectivity conflicts according to their scope without changing nets to remove the difference.

## Power stages and distance

For buck and similar stages, distinguish input hot loops, switch nodes, outputs, and feedback. Lines between nearby component centers are not actual loops. PCB netlists cannot fully establish internal IC conduction paths or unrouted copper geometry.

Compare distances using actually transformed pads and explicit relationships. Maximum distances, same-side requirements, and keepouts follow project clauses rather than universal millimeter thresholds.

## Coverage limits

Placement poses cannot certify loop inductance, switch-node copper area, EMI, compensation stability, actual impedance/delay/return paths, or thermal performance. Check and record declared local placement requirements; list other suggestions and unverified scope separately.
