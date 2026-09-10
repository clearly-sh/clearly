# Security

## Reporting a vulnerability

Use GitHub's private reporting: **[Report a vulnerability](https://github.com/clearly-sh/clearly/security/advisories/new)**.
It reaches us directly and stays private until we publish an advisory.

Please do not open a public issue for a security report.

Include what you did, what happened, and what you expected. A proof of concept helps; you do not
need a working exploit for us to take it seriously. We will acknowledge receipt, keep you updated
while we work, and credit you when it is fixed unless you would rather we did not.

Please give us a reasonable window to ship a fix before disclosing publicly.

## What is in scope

- `relay.clearly.sh` — the MCP server and the workspace API
- `clearly.sh` / `www.clearly.sh` — the web application
- `files.clearly.sh` — file serving
- the `beehaven` CLI and its local daemon

Especially interesting to us: anything that lets one workspace reach another's data, anything that
lets a token exceed its granted scopes, and anything that returns a paid artifact to a caller who
has not paid for it.

## What an MCP connection can do

Worth stating plainly, because connecting an agent to a workspace is a real grant.

A token carries **scopes** — `rpc:read`, `rpc:write`, `agent:ask` — and is bound to **one workspace
and one agent**. It cannot address a sibling workspace unless it also carries `rpc:admin`, which
ordinary tokens do not. An unbound credential is refused rather than silently resolved to a
default.

The agent segment in `/mcp/w/<workspace>/a/<agent>` is **addressing, not authority**: the server
refuses it unless it matches the token's own agent, so editing the URL cannot change who a
connection acts as.

Tokens reaching your **local machine** (`/local` paths, which run commands and touch real files)
require `rpc:admin` and are refused for everything else. That gate is at the server boundary, not
in the client.

You can review and revoke connections in Settings → Developers.

## Handling of reports about AI output

An agent writing something wrong, or being persuaded to say something odd by content it read, is a
quality issue rather than a vulnerability — open a normal issue for it. It becomes a security
report when model behaviour crosses a **trust boundary**: reading data from another workspace,
escalating its own scopes, or causing a write the connecting user never authorised. Those we want
by email.
