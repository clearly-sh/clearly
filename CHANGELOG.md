# Changelog

Notable changes to the Clearly MCP server and the `beehaven` CLI.

The MCP server is a hosted service — **deploying it is the release**, so it has no install step and
no version to pin. It reports its build in the `initialize` handshake as
`serverInfo.version` = `<version>+<commit>`; that string is what to quote in a bug report.

## MCP server

### 0.3.1

- **Protocol revisions are negotiated, not asserted.** The server now echoes the revision the
  client asked for when it supports it, and answers with its own only when it does not — which is
  what the spec requires. Supported: `2025-06-18`, `2025-03-26`, `2024-11-05`. Previously every
  client was answered `2025-06-18` regardless of what it requested, which a strict client may treat
  as "I cannot speak your dialect" and disconnect on.
- **`serverInfo.version` is real.** It was a hand-typed constant that nothing bumped; it now
  carries the deployed build and commit.
- `GET /mcp` answers `405` rather than `200`, as the transport expects.

### 0.3.0

- **Every credential is bound to a workspace *and* an agent.** Sign-in asks for both. A token that
  names neither is refused rather than silently resolved to a default, so the activity log can
  always say who acted.
- `/mcp/w/<workspaceId>` and `/mcp/w/<workspaceId>/a/<agentId>` — extra path forms so a client that
  stores one credential per server entry can hold several workspaces or agents at once. The agent
  segment is addressing only; the server refuses it unless it matches the token's own agent.

### 0.2.x

- Cross-workspace dispatch requires `rpc:admin`. Ordinary tokens can enumerate the workspaces you
  belong to but can only act in their own.
- `tools/list` trimmed from 139 tools to ~20. Everything else stayed reachable through
  `clearly_workspace_catalog` → `clearly_workspace_invoke`, which costs a call when you need it
  instead of ~27k tokens of context on every session.

## CLI

### 0.8.x

- Switching workspaces works from the CLI. `beehaven connect team/<id>` and
  `connect workspace/<id>` previously failed on the *next* command with `teamId is required` — an
  error naming a field you never typed, one command after its cause.
- Every call is attributed to an agent identity. An expired identity is refused rather than falling
  through to the account owner.
- `beehaven sql`, `ls`, `tree` no longer need developer mode.

---

Older entries are not published. If you need to know whether a specific change is live, the
`serverInfo.version` from your own `initialize` is authoritative.
