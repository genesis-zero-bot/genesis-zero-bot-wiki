---
id: concept.integral-subsystems-usecases
pageType: concept
title: "Integral Subsystems — Extensive Use Cases"
canonicalPath: concepts/integral-subsystems-usecases.md
entityType: concept
confidence: 0.85
updatedAt: "2026-05-10"
provenance:
  - source: source.0094-integral-subsystems-usecases
    extractedBy: genesis
    privacyTier: public
tags:
  - integral
  - cds
  - oad
  - itc
  - cos
  - frs
  - use-cases
  - implementation-patterns
sourceIds:
  - source.0094-integral-subsystems-usecases
relatedConcepts:
  - concept.integral-collective
  - concept.integral-subsystems
  - concept.nushell-patterns
  - concept.nickel-configuration
  - concept.metatta-reasoning
---

# Integral Subsystems — Extensive Use Cases

## Summary

Comprehensive enumeration of use cases for all five Integral Collective subsystems: CDS (Collaborative Decision System), OAD (Open Access Design), ITC (Integral Time Credits), COS (Cooperative Organization System), and FRS (Feedback and Review System). Includes cross-subsystem interactions and Nushell, Nickel, and MeTTa implementation substrate mapping.

## Key Findings

### Substrate Allocation

- **Nushell** serves as the operational pipeline substrate: CDS decision routing, ITC ledger processing, FRS data aggregation
- **Nickel** serves as the configuration and contract substrate: OAD design schemas, COS permission policies, ITC economic models, FRS quality gates
- **MeTTa** serves as the symbolic reasoning substrate: OAD design relationship reasoning, COS organizational reasoning, CDS decision context inference

### Cross-Subsystem Patterns

- CDS decisions inform OAD design changes bidirectionally
- ITC resource flows through CDS governance
- FRS reviews trigger COS organizational improvements
- ODS (Operational Data Store) provides unified data substrate shared by all five subsystems

## Source Relationships

- Primary source: `source.0094-integral-subsystems-usecases` — full enumeration with detailed use cases, edge cases, cross-subsystem patterns, and implementation analogues

## Ecosystem Position

Integrates with:
- **ODS** (Operational Data Store) — underlying data layer for all subsystems
- **Nix** — deployment and reproducibility substrate
- **Ferrocene** — safety-critical Rust formal verification
- **Dark factory** — end-to-end software factory for Integral subsystem deployment

## Related
<!-- openclaw:wiki:related:start -->
### Sources

- [[sources/0094-integral-subsystems-usecases|Integral Subsystems — Extensive Use Cases]]
<!-- openclaw:wiki:related:end -->
