# Safe-by-Default Agent Template

## Intent

Every new agent in the Olympus ecosystem starts from this template. It encodes the principle of least privilege at the atom level: zero tools, read-only capability, strict isolation, and three hard role-boundary refusals baked in before you add anything.

The template is a real, valid composition — not pseudocode. Fork it and remove nothing without justification.

## How to use

1. Fork `agents/safe-by-default.json` into `agents/<your-slug>.json`
2. Set `id` to your agent slug (lowercase, hyphens, matches the filename stem)
3. Set `name`, `description`, and `tags` for your agent
4. Set `persona` to the appropriate persona-atoms composition
5. Add ONLY the tool-definition atoms your agent needs — each addition expands the attack surface
6. Add ONLY the capability-declaration atoms your agent needs — prefer narrower over broader grants
7. Replace `isolation` with a less strict variant ONLY if your workload requires it, and document why in the commit message

## Security properties

| Property | Value | Why |
|---|---|---|
| Tools | `[]` (empty) | No external side effects by default |
| Capabilities | `read-only-workspace` | Agents should not mutate state they don't own |
| Role boundaries | no-exec + no-network + no-exfiltration | Belt-and-suspenders refusals on the three highest-risk surface areas |
| Isolation | `read-only-sandbox` | Subprocess with read-only FS mount, no network, scoped to workspace root |

## Common extensions

The table below maps a capability need to its minimum expansion. Never jump past the minimum.

| Need | Add to `tools` | Add to `capabilities` | Change `isolation` |
|---|---|---|---|
| Read repo files | _(none — already granted)_ | _(already included)_ | _(unchanged)_ |
| Execute shell commands | `bash-exec` | `exec-with-approval` | `seccomp-restricted` |
| Write files | `file-write` | `file-write-scoped` | _(unchanged)_ |
| Fetch a URL | `http-fetch` | `network-read-only` | `network-namespaced` |
| POST to an endpoint | `http-post` | `network-full` | `container-with-allowlist` |
| Query a database | `sql-query` | `db-read-only` | _(unchanged)_ |
| Mutate a database | `sql-mutate` | `db-read-write` | `ephemeral-vm` |

## Anti-patterns

- **Adding all tools "just in case."** Tools not listed cannot be invoked. Lean empty list stays lean.
- **Removing role boundaries before removing tools.** Remove the tool; the role-boundary refusal is defense-in-depth and costs nothing to keep.
- **Upgrading isolation without upgrading the rationale.** If you move from `read-only-sandbox` to `container-with-allowlist`, the commit message must name why.
- **Shipping a template fork without changing `id`.** The validator will reject two compositions with the same `id` stem.

## Validation

```bash
python3 scripts/validate.py   # must exit 0 before any PR
python3 scripts/build-exports.py   # regenerates exports/
```
