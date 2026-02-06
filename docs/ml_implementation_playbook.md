# ML Implementation Playbook (Computronium)

This playbook defines the minimum implementation path for effective materials search using the Computronium workflow.

## 1) Objective and Success Criteria

Objective:
- rank candidate materials with ML surrogates,
- enforce chemistry/physics constraints,
- escalate top candidates to DFT,
- publish auditable evidence for every promoted candidate.

Success criteria:
- reproducible candidate ranking from fixed inputs,
- uncertainty-aware promotion rule (not top-score only),
- DFT handoff package generated per promoted candidate,
- machine-verifiable certificate artifacts attached to each run.

## 2) Required Model Lanes

Use at least one model from each lane:
- Interatomic potential / property surrogate: MACE or M3GNet/MatGL
- Descriptor baseline lane: composition/structure feature model (tree/linear baseline)
- Uncertainty lane: ensemble variance or MC-dropout equivalent

The production decision must use both predicted value and uncertainty.

## 3) Candidate Evaluation Policy

For each candidate `c`:
- predict target property score `s(c)`
- estimate uncertainty `u(c)`
- compute promotion score `p(c) = s(c) - lambda * u(c)` for maximize objectives
- apply hard constraints before ranking (charge balance, stoichiometry bounds, stability bounds)

Only candidates passing hard constraints can enter DFT promotion queue.

## 4) DFT Promotion Gate

Promote candidate if all are true:
- passes all hard constraints,
- in top-K by promotion score,
- uncertainty below configured maximum,
- novelty/diversity guard passes (avoid near-duplicates).

Output a DFT handoff object containing:
- structure representation,
- model predictions and uncertainty,
- gate decision trace,
- provenance digest.

## 5) Audit and Provenance Requirements

Every run should produce:
- model configuration snapshot,
- candidate scoring table,
- constraint evaluation trace,
- promotion decisions,
- immutable hash manifest,
- certificate bundle pointers.

Do not ship results without these artifacts.

## 6) Minimal Daily Operator Checklist

1. Validate model/config digests.
2. Run surrogate scoring on current candidate pool.
3. Run constraint gate and promotion scoring.
4. Export DFT handoff packages for promoted set.
5. Generate/attach evidence artifacts and certificates.
6. Record run summary and hashes.

## 7) Integration Notes

- Keep surrogate inference and DFT preparation decoupled.
- Treat model updates as versioned events with explicit rollback path.
- Never overwrite prior evidence bundles; append only.
- Keep all promotion thresholds explicit and versioned.
