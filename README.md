# Clearly

**Your workspace, addressable by agents.** Documents, canvases, slides, sheets, projects and
tickets — reachable over [MCP](https://modelcontextprotocol.io) from any client, and from a
terminal.

- **Website** — [clearly.sh](https://clearly.sh)
- **MCP docs** — [clearly.sh/docs/mcp](https://clearly.sh/docs/mcp)
- **MCP Registry** — [`sh.clearly/clearly`](https://registry.modelcontextprotocol.io/v0.1/servers?search=sh.clearly)

> **This repository has no source code.** It is the public home for **issues, release notes and
> security reports**. Clearly is a hosted service; the server runs on our infrastructure and the
> CLI is distributed as a build. If you are looking for a place to file a bug or ask what changed
> in a release, you are in the right place.

---

## MCP

Clearly is an MCP **server**, not a plugin — your agent connects out to it.

```
https://relay.clearly.sh/mcp
```

Authentication is OAuth 2.1 with dynamic client registration ([RFC 7591](https://datatracker.ietf.org/doc/html/rfc7591)),
so most clients need nothing but the URL. Sign-in asks which **workspace** and which **agent** the
connection acts as.

### Claude Code

```bash
claude mcp add --transport http clearly https://relay.clearly.sh/mcp
claude mcp login clearly
```

There is also a [Claude Code plugin](https://github.com/clearly-sh/clearly-plugin) that bundles the
connector with a set of skills.

### Other clients

Any client that speaks streamable HTTP MCP can connect to the same URL. Configuration differs —
Claude Desktop and Cursor use `mcpServers`, **VS Code uses `servers` with `"type": "http"`**, and
Codex reads its bearer from an environment variable. The
[MCP docs](https://clearly.sh/docs/mcp) carry a snippet per client.

### Holding more than one workspace or agent at once

A client stores **one credential per server entry**, so two workspaces behind a single entry
collide. Two extra path forms exist for that reason — each is a distinct credential key:

| URL | Means |
|---|---|
| `/mcp` | the workspace and agent chosen at sign-in |
| `/mcp/w/<workspaceId>` | pinned to one workspace |
| `/mcp/w/<workspaceId>/a/<agentId>` | pinned to one workspace **and** one agent |

The agent segment is **addressing, never authority** — the server refuses it unless it matches the
token's own agent.

### Protocol

Streamable HTTP. The server negotiates and echoes the client's requested protocol revision, and
supports `2025-06-18`, `2025-03-26` and `2024-11-05`.

---

## CLI

```bash
curl -fsSL https://clearly.sh/install.sh | sh
```

The installer writes `beehaven` to `~/.local/bin` (override with `BEEHAVEN_BIN_DIR`).

Read it before you run it — [`install.sh`](https://clearly.sh/install.sh) is a plain shell script.
Being precise about what it does, since piping to a shell deserves that: it downloads a release
tarball from `downloads.clearly.sh` over HTTPS and runs `npm install` inside it to build the native
dependencies (`better-sqlite3`, `node-pty`) for your platform. **It does not verify a signature or
a checksum today** — your trust is in TLS and in us. Node 20+ is required and the script checks
for it first.

```bash
beehaven login                       # authenticate
beehaven connect home                # pick a workspace
beehaven ls ~                        # your documents, as files
beehaven grep -ril pricing ~         # search them
beehaven call <action> '{...}'       # any workspace action
beehaven status                      # connection + auth state
```

`beehaven --help` lists the rest.

---

## Reporting things

- **Bugs and feature requests** — [open an issue](https://github.com/clearly-sh/clearly/issues/new/choose).
- **Security** — please do **not** open a public issue. See [SECURITY.md](SECURITY.md).

When filing an MCP issue, `beehaven status` and the output of the failing `initialize` are the two
most useful things to include. The server reports its build in `serverInfo.version`
(`0.3.1+<commit>`), which tells us exactly what you were talking to.

---

## Licence

[MIT](LICENSE) for the contents of this repository. The Clearly service itself is governed by its
[terms](https://clearly.sh/terms).
