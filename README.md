# Enterprise Context Protocol (ECP)

**A thin protocol for giving agents the context they keep failing to find.**

Agents fail in enterprises for two reasons, in this order:

1. **Addressing** — they cannot find the table, metric, or definition that the user's words refer to.
2. **Reference** — even when they find an entity, they lack the background (owner, scope, caveat, history) to use it correctly.

RAG attacks neither problem directly. ECP is a minimal, implementable protocol for both: a **term-resolution layer** for addressing, and a **context envelope** for reference — designed to sit *beside* your existing data platform, not replace it.

> Positioned as a sibling to MCP: MCP moves *tools* to agents; ECP moves *addressable context*. An agent typically calls ECP to resolve "monthly active users" → a governed metric ID, then calls MCP tools to query it.

## The protocol, in one page

### 1. Term resolution (the addressing half)

```
resolve(term, context?) →
  [
    { id, kind: "metric" | "table" | "dimension" | "entity",
      score, definition, owner, caveats[] }
  ]
```

Rules that make it work:

- **Refuse over guess.** Below confidence threshold, return near-misses *labeled as near-misses*. A wrong-but-confident resolution is worse than a question back.
- **Caveats are first-class.** Two metrics with the same name and different time grains are *both* returned, with the difference stated. Hiding the ambiguity is the bug.
- **SSOT by id, alias by term.** One canonical id per governed object; unlimited aliases resolve to it.

### 2. Context envelope (the reference half)

Every resolution carries a bounded envelope:

```
{ id, definition, owner, scope: { timeGrain, filters, boundaries },
  status: "governed" | "exploratory" | "deprecated",
  lineage: { upstream: number, downstream: number } }
```

- **Tier model.** `governed` (curated, owner-reviewed) vs `exploratory` (auto-registered, unaudited). Agents may answer from exploratory context but must label it as such; a promotion pipeline (usage + review) moves objects up a tier — the ladder, not a wall.
- **Bounded by default.** The envelope is what fits in a prompt slice, not a data dump.

### 3. Read protocol, write via governance

Agents and humans *read* ECP freely. Writing (new terms, promotions, corrections) flows through review — because context that anyone can silently mutate is context nobody can trust.

## What ECP is not

- Not a knowledge graph. It is a thin address layer; graph-shaped detail can live behind it.
- Not a replacement for your semantic layer. It is the *protocol face* of whatever semantic inventory you already have (metrics platform, data catalog, or ontology).
- Not prompt content. It is infrastructure: ids, contracts, and review gates.

## Repository layout

```
enterprise-context-protocol/
├── spec/            # the normative protocol documents (versioned)
│   ├── 00-overview.md
│   ├── 01-resolution.md
│   └── 02-envelope.md
├── reference/       # minimal reference implementation (planned)
└── docs/            # design rationale, comparison with RAG/ontology approaches
```

## Status

**v0 — spec drafts.** The problem statement and protocol shape are stable; field names and conformance levels are open for discussion. Issues and implementation war stories welcome.

## License

[MIT](LICENSE) — spec and reference code. Implement freely; conform by citing the version.
