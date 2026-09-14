# Start Here: The Product Catalog & Hardened Images

```text no-run-button
   The same app, one line changed in the Dockerfile

   FROM node:20                        FROM dhi.io/node:20-hardened
     47 CVEs  (2C 12H 20M 13L)   ──▶     2 CVEs  (0C 0H 1M 1L)
     fat base, 431 packages             minimal · signed · SBOM + provenance
```

*This lab is about **containing what an AI agent can touch** - your network, files,
secrets, and tools. But before we lock down what the agent can reach, let's make
sure what it **builds** starts clean. That's Docker Hardened Images (DHI).*

Throughout this lab we use one running example: a **Product Catalog** app (a Node.js
service backed by PostgreSQL, S3, and Kafka). In the earlier labs, an agent
containerised it and picked `FROM node:20` on its own - a fat base that dragged in
**431 packages and 47 CVEs**. Let's recap the one-line fix, then move on to policies.

> [!NOTE]
> Everything here is **simulated** - no real Docker, `sbx` daemon, or network - so
> every learner sees the same numbers and the same allow/deny decisions.

## Step 1 - See the problem

Scan the image the agent built. Most findings trace back to the base image, not the
app code:

```bash
docker scout cves product-catalog:latest
```

**47 vulnerabilities**, including 2 critical and 12 high. The app is fine; the fat
`node:20` base is the liability.

## Step 2 - Change one line

Ask Gordon (`docker ai`) to swap the base for a **Docker Hardened Image** and
rebuild. It keeps the multi-stage layout - a `-dev` variant to build, the distroless
`-hardened` variant at runtime:

```bash
docker ai "Switch the Dockerfile to a Docker Hardened Image base and rebuild"
```

## Step 3 - See the fix

Re-scan the rebuilt image:

```bash
docker scout cves product-catalog:latest
```

The count collapses to **2 (0 critical, 0 high)**. Same app, a fraction of the
attack surface - and a **hardened base** is minimal, signed, and continuously
patched, so you can *require* it, not just recommend it.

## Why this comes first

A clean base is the floor, not the ceiling. Even a perfectly hardened image runs
**as the developer who launched the agent** - with their files, their secrets, their
network. Harden the image *and* contain the agent, and you've closed both ends.

That containment is the rest of this lab. Next: **the policy model** - how an org
authors one set of rules and pushes them to every developer's sandbox.
