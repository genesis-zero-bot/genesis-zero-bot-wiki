---
title: "Data-Centric Architecture Forum"
type: source
url: https://www.dcaforum.com/
status: active
updated: 2026-05-23
privacyTier: public
---

# Data-Centric Architecture Forum (DCAF)

Annual practitioner conference (June, Fort Collins CO) since 2019. No booths, no vendor pitches, no buzzwords. Founded and organized by Semantic Arts.

Format: 3 days, small room, 60-min presentations + 30-min discussion, live demos from vetted implementors.

2026: June 9-11, Fort Collins CO. Registration open.

## Key Themes (2023-2026)

**2023:** "The Last Mile" — integrating 10 core capabilities into architecture (auth, entity/URI resolution, federation, constraint management, model-driven UIs, SemOps).

**2024:** "From Hype to Architecture" — LLM + KG integration, cybersecurity, provenance via RDF-star, entity resolution at scale, live demos (IndustryKG, SemSpect, Zazuko).

**2025:** "Scale, Speed, Strings" — Scale to 10^12 triples, near-real-time queries, KGraphRAG (graph-first RAG vs document-first).

**2026:** Cybersecurity, Validity/Grounding AI (SHACL, medallion bronze/silver/gold, entity resolution, provenance, audit trails), AI + Semantic (AI for data alignment, ontology production, conversational AI as query builder, KGs in agentic environments).

## Key People

**Dave McComb — Semantic Arts** — Author: The Data-Centric Revolution. Thesis: "Data is the enduring asset; applications are ephemeral." Developed gist ontology (~100 classes, freely available).

**Jans Aasman — Franz Inc** — AllegroGraph: high-performance semantic graph database. GraphTalker (v9.0): AI agent for natural language interaction with enterprise KGs.

**Paco Nathan — Senzing** — Entity Resolved Knowledge Graphs (ERKG): entity resolution as the "missing ingredient" for knowledge graphs.

**Mark van Berkel — Schema App** — Semantic AI layer via schema markup. MCP Server: governed semantic interface preventing AI hallucinations.

## Relevant Patterns

- **KGraphRAG:** Graph-first RAG (not document-first). Build graph from structured data first, then add vector search.
- **Entity Resolution:** Real-time ER for member/resource graphs without duplicates.
- **Medallion Architecture:** Bronze/silver/gold data quality layers.
- **MCP:** Model Context Protocol for AI agents consuming governed semantic data.
- **SHACL:** W3C standard for machine-readable constraint shapes.
- **RDF-star:** Provenance tracking for governance decisions.

## Application to RegenTribes

- MyCoNet knowledge layer: KGraphRAG pattern over document-first
- Member graph: entity resolution for 38-380+ member scaling
- Greenhollow 5-pillar: medallion maturity levels
- Community agreements (M06): SHACL constraint enforcement
- Genesis AI: MCP semantic layer preventing hallucinated governance advice
- Governance records: RDF-star provenance chain for SPARK/PROVE/BUILD/LIVE phases

## References

- https://www.dcaforum.com/
- https://github.com/semanticarts/gist
- https://franz.com/allegrograph/
- https://senzing.com/entity-resolved-knowledge-graphs/
- https://www.schemaapp.com/