# An Agent Containerises It

```text no-run-button
   "containerise this app" ──▶ agent ──▶ product-catalog:latest
   multi-stage · npm ci · non-root · .dockerignore   …still on fat node:20
```

*Hand a real app to a coding agent and it containerises it **well**. This is the
agent at its best - but "best practices" can't fix the base it chose.*

## Step 1 - Ask the agent

`docker ai` is Gordon, Docker's built-in agent:

```bash
docker ai "Containerise the product catalog for production following best practices"
```

> [!NOTE]
> Prefer Claude? Run `claude`, then ask it to *"containerise the product catalog
> following best practices"* - same result.

## Step 2 - See what it wrote

```bash
tree product-catalog-demo-showcase
```

```bash
cat product-catalog-demo-showcase/Dockerfile
```

Multi-stage, non-root, tidy - a genuinely well-formed build.

## Step 3 - Best practices ≠ a clean image

It still starts `FROM node:20` - the convenient, fat base. Best-practice
*layering* can't undo what the *base* drags in.

> [!IMPORTANT]
> An agent applying every best practice still ships whatever CVEs live in the
> base it picked. You can't lint your way out of a vulnerable base - you
> **measure it**, then **swap it**. That's **Lab 2**.

But the same tool, pointed at your machine instead of your Dockerfile, is a very
different story. Next: **The Same Agent, On Your Host**.
