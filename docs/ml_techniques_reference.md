# ML Techniques Reference (Computronium)

## Core techniques used

- Constraint-certified filtering before expensive simulation.
- Multi-lane surrogate ranking (primary + baseline).
- Uncertainty-aware promotion scoring.
- Diversity-aware top-K promotion.
- Evidence-first publication with hash-anchored manifests.

## Recommended defaults

- Top-K promotion: 50-200 depending on budget.
- Uncertainty penalty `lambda`: tune on historical hit-rate.
- Diversity threshold: cluster candidates by fingerprint and cap per cluster.
- Re-train cadence: event-driven (new DFT labels), not fixed-time only.

## Failure modes to avoid

- selecting by raw predicted score only,
- ignoring uncertainty under distribution shift,
- skipping hard constraints for speed,
- publishing claims without linked evidence hashes.

## Practical implementation pattern

1. Ingest candidates.
2. Score with surrogate lane(s).
3. Compute uncertainty.
4. Apply hard constraints.
5. Rank with uncertainty penalty.
6. Apply diversity control.
7. Emit DFT handoff + evidence manifests.
8. Generate certificate bundle references.
