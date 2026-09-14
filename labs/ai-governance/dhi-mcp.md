# DHI MCP: Hardened Servers You Don't Have to Build

```text no-run-button
   Docker Hardened Images  →  clean BASE for what the agent builds
   DHI MCP Catalog         →  clean TOOLS for what the agent calls

   docker/mcp-catalog-dhi  ──▶  11 pre-hardened MCP servers
                                 each: DHI base · SBOM · provenance · signed
```

*You've hardened the **image** the agent builds. The same idea applies to the
**tools** the agent calls. An MCP server is just another container - so it can carry
CVEs, run as root, or be tampered with. Docker ships a catalog of **pre-hardened MCP
servers** so you don't have to build and vet them yourself.*

In plain terms: instead of grabbing some random MCP server off the internet and
trusting it, you pull servers from Docker's **hardened catalog**, and the gateway
**verifies each one is signed before it runs**. Same trust story as DHI, applied to
tools.

## Step 1 - Pull the hardened catalog

```bash
docker mcp catalog pull docker/mcp-catalog-dhi
```

Eleven servers, all signed by Docker, Inc. - the signature check happens on pull.

## Step 2 - See what's inside

```bash
docker mcp catalog server ls docker/mcp-catalog-dhi
```

These are the same everyday servers you'd reach for - `filesystem`, `github`, `git`,
`fetch`, `memory` - except each is built on a **DHI base** and ships an **SBOM,
provenance, and signature**. In Docker Desktop's MCP Toolkit they show up labelled
**(Hardened)**.

## Step 3 - Run one through the gateway

Start a server from the catalog. The gateway **verifies the signature and provenance
before the server ever runs**, and runs it locked down (read-only rootfs, all
capabilities dropped, secrets mounted only into the target):

```bash
docker mcp gateway run --catalog docker/mcp-catalog-dhi --server filesystem
```

The key line: *a hardened server you did not have to build - verified before it ran.*
An unsigned or tampered server image would be **refused right here**, before it could
touch anything.

## Why this matters

| Without DHI MCP | With DHI MCP |
| --- | --- |
| Agent wires up any MCP server it finds | Servers come from a **hardened, signed catalog** |
| Server may carry CVEs, run as root | Each on a **DHI base**, non-root, minimal |
| No proof of what's inside | **SBOM + provenance + signature**, verified at the gateway |

A hardened catalog decides *which servers are trustworthy to run*. The next question
is **which tools an agent may actually call** from them - and that's authored by the
org as policy. Head to **MCP Governance**.
