# XAIP: Agent Composition Schema

## What an agent composition is

An agent composition in agent-atoms assembles persona, tool-definition, capability-declaration, role-boundary, and isolation-constraint atoms into a complete agent definition that a runtime (Olympus, aish, AutoGen) can instantiate.

## Composition structure

An agent composition references atoms by ID:

```json
{
  "schema": "https://agent-atoms.com/schemas/composition-v1.json",
  "type": "agent",
  "id": "my-agent-slug",
  "version": "1.0.0",
  "name": "My Agent",
  "references": {
    "persona":         { "ref": "agent-atoms://atoms/persona/my-persona",        "version": "1.0.0" },
    "tools":           [{ "ref": "agent-atoms://atoms/tool-definition/my-tool",  "version": "1.0.0" }],
    "capabilities":    [{ "ref": "agent-atoms://atoms/capability-declaration/my-cap", "version": "1.0.0" }],
    "role_boundaries": [{ "ref": "agent-atoms://atoms/role-boundary/my-boundary", "version": "1.0.0" }],
    "isolation":       { "ref": "agent-atoms://atoms/isolation-constraint/my-isolation", "version": "1.0.0" }
  }
}
```

The `persona` and `isolation` references are required. All others are optional arrays.

## Multi-agent compositions (swarms)

For swarms, set `type` to `"swarm"` and include a `sub_agents` array alongside a `coordinator` reference:

```json
{
  "schema": "https://agent-atoms.com/schemas/composition-v1.json",
  "type": "swarm",
  "id": "research-and-code-swarm",
  "version": "1.0.0",
  "name": "Research and Code Swarm",
  "coordinator": { "ref": "agent-atoms://atoms/persona/coordinator-persona", "version": "1.0.0" },
  "sub_agents": [
    { "ref": "agent-atoms://atoms/persona/researcher-persona", "version": "1.0.0", "role": "researcher" },
    { "ref": "agent-atoms://atoms/persona/coder-persona",      "version": "1.0.0", "role": "coder" }
  ],
  "coordination_protocol": "sequential"
}
```

`coordination_protocol` accepts: `"parallel"`, `"sequential"`, or `"hierarchical"`.

- **parallel** — sub-agents run concurrently with no ordering guarantee; results are merged by the coordinator.
- **sequential** — sub-agents run in declaration order; each receives the prior agent's output.
- **hierarchical** — the coordinator delegates to sub-agents dynamically based on task decomposition.

## Cross-catalog integration

| Catalog | Role in agent composition |
|---|---|
| persona-atoms | Identity layer for each agent; `persona` reference is required |
| prompt-atoms | System prompt fragments assembled per persona at instantiation time |
| policy-atoms | Policy floor gates what the agent is permitted to do |
| plugin-atoms | Plugins extend agent capabilities at runtime beyond declared tool-definitions |

## Schema reference

The composition schema lives at `schemas/composition-v1.json`. Key constraints:

- `id` must be a kebab-case slug: `^[a-z0-9][a-z0-9-]{1,62}[a-z0-9]$`
- `type` is `"agent"` (single agent) or `"swarm"` (multi-agent composition)
- `references.persona` and `references.isolation` are required for `type: agent`
- `coordinator` and `sub_agents` are used for `type: swarm`
- All `ref` URIs follow the pattern `agent-atoms://atoms/<atom-type>/<slug>`
