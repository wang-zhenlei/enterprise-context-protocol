# ECP-00: Overview

**Status:** draft v0. This document is informative; `01-resolution.md` and `02-envelope.md` are normative.

## Problem

Enterprise data agents fail at *addressing* first, *reference* second:

1. **Addressing.** "Monthly active users" is 4 different metrics in a typical enterprise. Retrieval over documents returns prose about the term, not a resolvable handle to the governed object.
2. **Reference.** Even a correct handle lacks context: which owner, which time grain, which exclusions, is it governed or experimental?

ECP defines a minimal read/write protocol for both, designed to wrap an existing semantic inventory (metric platform, catalog, ontology) rather than replace it.

## Positioning

| Layer | Moves to agents | ECP relationship |
|---|---|---|
| MCP | tools (queries, search, execution) | sibling — resolve via ECP, act via MCP |
| RAG | unstructured documents | complement — ECP handles governed objects; RAG handles the rest |
| Ontology / semantic layer | rich structure | backend — ECP is its protocol face |

## Conformance levels

- **L1 resolver** — implements `resolve()` with refuse-over-guess semantics.
- **L2 envelope** — resolution results carry the bounded context envelope.
- **L3 governed write** — term creation/promotion flows through review with audit trail.

An implementation states its level; agents degrade gracefully against lower levels.

## Versioning

The spec is versioned `ECP/0.x` while draft. Conformance claims cite the exact version.
