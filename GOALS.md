# agent-atoms — Goals

> Agent primitives canonicalized — personas, tool definitions, capability declarations, role boundaries, isolation constraints — across LangChain, AutoGen, CrewAI, Olympus and beyond.

*This document is derived from `aish/ARCHITECTURE.md` (now `xdao/xdao/ARCHITECTURE.md` §The *-Atoms Catalogs). Sections marked **Generated** are pattern-based and are intended as a starting point for revision, not as decided plan.*

---

## What this catalog makes civilization-grade

The agent space is exploding with no canonical catalog. Every framework (LangChain, AutoGen, CrewAI, Olympus) reinvents personas, tool definitions, and capability declarations. Porting an agent between frameworks is a rewrite. There's no shared library of safe defaults.

By cataloging the primitives, `agent-atoms` turns this domain from opaque-and-ephemeral to typed, versioned, composable, machine-readable, and open — the civilization-grade properties the ecosystem requires.

## What it catalogs

### Atom types

- **`persona`** — Agent voice, expertise, role — overlaps with prompt-atoms persona but with additional agent-specific fields (memory model, planner type, supervisor relationship).
- **`tool-definition`** — Tool schema (function name, args, return type, side effects).
- **`capability-declaration`** — What the agent is allowed to do (read files, exec commands, call network, request approval).
- **`role-boundary`** — What the agent is NOT allowed to do (escalation rules, refusal patterns).
- **`isolation-constraint`** — Process / network / filesystem isolation requirements.

### Compositions: `agents`

An agent composition assembles persona + tools + capabilities + role boundaries + isolation into a complete agent definition. Multi-agent compositions (swarms) compose individual agents with communication patterns and supervision hierarchies.

### Rule types

- **`capability-grant`** — How capabilities are granted (declared, runtime-elevated, user-approved).
- **`isolation-rule`** — Required isolation level per capability (network=true requires sandbox).
- **`communication-pattern`** — Permitted agent-to-agent communication (broadcast, request-response, supervisor-only).
- **`supervision-hierarchy`** — Who can supervise whom; escalation paths.

## Runtime consumers

- **olympus** — Pantheon Modules are agents. Olympus's whole architecture (Hermes routing, Mnemosyne memory, Aegis signing, Pantheon execution) is multi-agent. agent-atoms is its native vocabulary.

## Status & priority

**Current status:** `proposed`

**Priority tier:** Tier 2 — Highest priority to build next (runtime pull immediate)

**Trigger / activation condition:** Olympus is already pulling. Other agent frameworks adopt as the catalog matures.

## Roadmap *(Generated — milestone shapes mirror aish's roadmap pattern; revise as actual work begins)*

### v0.1 — Bootstrap & spec acceptance

**Goal:** Schema accepted. Olympus Pantheon Modules expressible as agent-atoms compositions.

**Success criterion:** Two Pantheon Modules defined entirely via agent-atoms; behavior identical to native definition.

**Kill criterion:** Pantheon Module customization can't be captured without per-module extensions — pivot to per-framework dialects.

**Work:**

- [ ] XAIP: agent composition schema with multi-agent compositions
- [ ] Define 5 atom type schemas
- [ ] Express 2 Pantheon Modules as agent-atoms
- [ ] Capability-grant runtime integration with Olympus
- [ ] Seed atoms: 10 personas, 20 tools, 8 capabilities, 5 role-boundaries

### v0.2 — Adoption & expansion

**Goal:** LangChain / AutoGen / CrewAI compilers.

**Work:**

- [ ] agent-atoms → LangChain agent compiler
- [ ] agent-atoms → AutoGen agent compiler
- [ ] Multi-agent composition schema for swarms
- [ ] Reference 'safe-by-default' agent template

### v1.0 — Operational

**Goal:** Default agent vocabulary across the AI agent ecosystem.

## Concrete atom example *(Generated — illustrative, not seed content)*

```yaml
agents/code-reviewer/definition.yml
---
id: code-reviewer
type: composition
version: 0.3.0
persona: { ref: atoms/persona/code-reviewer }
tools:
  - { ref: atoms/tool-definition/git-diff }
  - { ref: atoms/tool-definition/read-file }
capabilities:
  - { ref: atoms/capability-declaration/read-files }
role_boundaries:
  - { ref: atoms/role-boundary/no-code-execution }
isolation: { ref: atoms/isolation-constraint/read-only-sandbox }
```

## Adoption strategy *(Generated)*

Olympus pulls immediately. Reference compilers (→ LangChain, → AutoGen) bring adjacent communities.

## Civilization-grade property checklist

Every catalog must satisfy these before v1.0. Failing any blocks a release.

| Property | Mechanism in this catalog |
|---|---|
| Typed | JSON Schema in `schemas/` validates every atom, composition, rule |
| Versioned | Every atom has a semver `version` field; compositions reference atoms by version-pinned ID |
| Machine-readable | `exports/catalog.json` published on every release |
| Composable | Compositions reference atoms by ID; CI verifies references resolve and no circular dependencies |
| Open | Apache-2.0 licensed; LICENSE file present |
| Durable | No external dependencies for primary content (no remote image URLs, no vendor APIs in the hot path) |

## Related

- **Spec:** [atoms-spec](https://github.com/convergent-systems-co/atoms-spec) — the canonical structure every catalog conforms to
- **Tools:** [atoms-tools](https://github.com/convergent-systems-co/atoms-tools) — CLI for validate / export / bootstrap / resolve
- **Federation:** [xdao](https://github.com/convergent-systems-co/xdao) — ecosystem directory and discovery
- **Umbrella:** [atoms](https://github.com/convergent-systems-co/atoms) — every catalog as a git submodule
- **Manifest:** [`ATOMS.yml`](./ATOMS.yml) — this catalog's machine-readable manifest
- **Standard:** [`README.md`](./README.md) — catalog overview and contribution flow
