# Securing the Agentic Stack with Docker AI Governance — Simspace lab

An interactive, fully in-browser lab that follows one story end to end: **an AI
agent containerises a real app, then you secure the whole agentic stack around it.**
Everything in the terminal is simulated — no real Docker, `sbx` daemon, or network —
so it runs the same for everyone, with nothing to install.

This lab merges two workshops into a single narrative:

- **Securing the Agentic Stack** — Docker Scout, Docker Hardened Images (DHI), a
  signed + policy-gated CI pipeline, and agent sandboxing.
- **Docker AI Governance** — sandbox policies for network, filesystem, credential,
  and MCP-tool isolation.

## The arc

The lab opens with **Gordon** (`docker ai`) containerising the
[Product Catalog service](https://github.com/ajeetraina/product-catalog-demo-showcase),
then splits into two halves — *harden what the agent ships*, then *contain what the
agent can touch*:

| # | Section | What you do |
| --- | --- | --- |
| 0 | Why Secure the Agentic Stack | The two problems; set your org |
| 1 | **Gordon Containerises the Product Catalog** | **Start here** — `docker ai` builds a best-practice image (multi-stage, non-root) |
| 2 | Docker Scout: What's Inside | Scan it — the fat `node:20` base drags in 47 CVEs |
| 3 | Docker Hardened Images (DHI) | Swap the base, re-scan — CVEs collapse to `0C 0H 1M 1L` |
| 4 | Sign It, Then Gate It (CI) | A pipeline that fails closed on a Scout policy, then cosign-signs what passes |
| 5 | Contain the Agent: The Policy Model | How org policy flows: deny → allow → default-deny |
| 6 | Network Enforcement | Three `curl`s, three outcomes (404 / 403 / 403) |
| 7 | Filesystem Isolation | The agent can't read or mount your secrets (403 at creation) |
| 8 | Credential Isolation | The real key never enters the sandbox |
| 9 | MCP Governance | One governed gateway; gate tools with Cedar |
| 10 | Putting It All Together | One rogue agent, four attacks, one policy engine |
| 11 | Observability & Audit | The visibility half + governance-as-code (the API) |

The lab lives under [`labs/ai-governance/`](labs/ai-governance/): `labspace.yaml`
(config, seeded filesystem, the CI tab), `simulator.yaml` (command behaviour and the
`secure-build` CI workflow), and one markdown file per section. It's loaded at
runtime by a prebuilt image, so there's no build step for content.

**Interactive extras:** the **CI** tab (top-right) renders the `secure-build`
pipeline; the **Settings** dialog (⚙, next to Reset) exposes the org policies as live
toggles — *AI Governance enforced*, *deny exfiltration*, *deny credentials* — so
learners can flip a rule off and watch enforcement disappear.

## Author locally

You only need Docker.

```bash
docker compose up dev              # live preview at http://localhost:5173
docker compose run --rm validate   # validate the lab (fails on errors)
```

Edit the files under `labs/ai-governance/` and refresh the browser to see changes:

- `labspace.yaml` — title, catalog card, terminals, CI tab, seed files, sections, variables
- `simulator.yaml` — what each command does (scenarios), plus the CI `workflows:` catalog
- `*.md` — one file per section of instructions

Pin the toolchain to a released version for reproducibility:

```bash
export SIMSPACE_AUTHORING_IMAGE=dockersamples/simspace-authoring:1
```

## Deploy

**GitHub Pages (default):** enable Pages (Settings → Pages → Source: "GitHub
Actions"), then push to `main`. The workflow in
[`.github/workflows/deploy.yml`](.github/workflows/deploy.yml) validates the lab and
publishes it. Pull requests are validated first by
[`.github/workflows/validate.yml`](.github/workflows/validate.yml).

**As a container:** the [`Dockerfile`](Dockerfile) bases on the runtime image and
swaps in your lab.

```bash
docker build -t my-lab .
docker run --rm -p 8080:80 my-lab    # http://localhost:8080
```

## Authoring with an AI agent

This repo is set up for agent authoring. In Claude Code, an `authoring-lab` skill
(under `.claude/`) knows the workflow, `docker compose` / `validate-lab` are
pre-allowed, and a hook auto-validates the lab after every edit under `labs/`.
[`CLAUDE.md`](CLAUDE.md) loads the guide automatically.

## Learn more

See [`AGENTS.md`](AGENTS.md) for an authoring cheat-sheet, and the
[Simspace specs](https://github.com/dockersamples/simspace/tree/main/spec) for the
full `simulator.yaml` / `labspace.yaml` reference.
