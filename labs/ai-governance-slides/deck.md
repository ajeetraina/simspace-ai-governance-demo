<!-- chrome: false -->

<img src="assets/slide-01.webp" alt="Docker AI Governance — hands-on workshop title slide, presented by Docker and Redington" width="1600" height="900" loading="eager" fetchpriority="high" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: Welcome, everyone, to our **Docker AI Governance** hands-on workshop, run jointly by **Docker and Redington**. Over the next couple of hours we're going to move past the hype and get our hands dirty with the real question every team is now facing: how do you let AI agents do genuine work without letting them run wild? This is a working session, not a lecture — you'll have a live environment in front of you and we'll build the guardrails together. Let me start by introducing myself.

---

<!-- chrome: false -->

<img src="assets/slide-02.webp" alt="Meet your speaker — Ajeet Singh Raina, Developer Advocate at Docker, author of Operational AI with Docker" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: A quick word about who's guiding you today. I'm **Ajeet Singh Raina**, a Developer Advocate at Docker, with 20-plus years across system integration, testing, consulting, and DevRel. I'm a former **Docker Captain** and I lead a 17,000-plus member developer community in Bengaluru, and I co-authored **Operational AI with Docker** — the book on the right — which is exactly about deploying and operating agentic AI services responsibly. I mention the book not to sell it but because everything we'll do today comes from that same operational mindset: agents in production, not in slideware. Let's look at how the day is structured.

---

<!-- chrome: false -->

<img src="assets/slide-03.webp" alt="Workshop agenda — six sections: Autonomy requires guardrails, Enforced not advised, A layered approach, Sandboxing the agent, Protecting MCP, Audit scale and wrap-up" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: Here's our roadmap for today, six sections in a deliberate arc. We open with **Autonomy requires guardrails** to establish why agents need governance at all, then **Enforced, not advised** to draw the line between soft policy and hard mechanisms, and **A layered approach** to lay out the Docker governance stack. Then we roll up our sleeves: forty minutes on **Sandboxing the agent** — isolation, secrets, network, filesystem, and models — followed by **Protecting MCP** where we govern the tools agents call, and finally **Audit, scale & wrap-up** to give your security team the proof they'll ask for. Notice the two biggest blocks are hands-on; that's intentional, because governance you can't demonstrate isn't governance. Before we dive in, here are the four takeaways I want you leaving with.

---

<!-- chrome: false -->

<img src="assets/slide-04.webp" alt="What you'll learn today — four cards: why agents need governance, sandboxing agents in microVMs, governing MCP tools, audit and enforcing policy at scale" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: If you remember nothing else, remember these four things. First, **why agents need governance** — the failure modes are real and structural, not hypothetical. Second, **sandboxing agents in microVMs** so an agent runs inside a hard isolation boundary rather than loose on your host. Third, **governing MCP tools**, because the tools an agent can call are as much an attack surface as the agent itself. And fourth, **audit and enforcing policy at scale**, so this works for one developer and for a whole fleet. Everything on the agenda maps back to one of these four. Let's get you into the live environment.

---

<!-- chrome: false -->

<img src="assets/slide-05.webp" alt="Accessing a Simspace — section title slide" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: Now let's get you set up with your own hands-on environment. We call the interactive lab a **Simspace** — a safe, self-contained space where you can run the exact commands and policies we discuss without touching a production system. This is where the workshop stops being slides and starts being something you drive yourself. On the next slide I'll give you the link to open it.

---

<!-- chrome: false -->

<img src="assets/slide-06.webp" alt="Access the Simspace — link to github.com/ajeetraina/simspace-ai-governance-demo" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: Here's your entry point — **github.com/ajeetraina/simspace-ai-governance-demo**. Go ahead and open that now; everything we do today lives in this repo, including the labs, the policies, and the running Product Catalog example we'll thread through the whole session. Take a moment to make sure the page loads for you before we move on, and flag me if anyone's stuck. Once you're in, we can start with the real question behind this entire workshop: why autonomy demands guardrails.

---

<!-- chrome: false -->

<img src="assets/slide-07.webp" alt="Section title — Autonomy requires guardrails" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: This is section one: **Autonomy requires guardrails**. The whole premise here is simple but easy to underestimate — the more independently an agent can act, the more it needs boundaries that it cannot cross. Autonomy without guardrails isn't productivity, it's unmanaged risk, and today's agents are far more autonomous than most people realize. Over the next few slides I'll show you exactly what agents are already doing in real teams, and why that changes the security picture. Let's start with where they're delivering real value.

---

<!-- chrome: false -->

<img src="assets/slide-08.webp" alt="AI Agents are here and doing real work — three columns: Engineering ships PRs, Marketing pulls CRM data and launches campaigns, Finance reconciles reports and queries systems live" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: Let's be clear-eyed: **agents are already doing real work** across the business, not just answering questions. In **engineering**, they read whole codebases and ship pull requests with no human in the loop. In **marketing**, they pull CRM data and launch campaigns end to end, moving from research to send. And in **finance**, they reconcile reports and query live systems, closing the loop between ledger, dashboard, and decision. The common thread is that each of these agents has real access to real systems and takes real actions. That's the value — and, as we'll see next, that's exactly where the danger begins.

---

<!-- chrome: false -->

<img src="assets/slide-09.webp" alt="Then came Claws — agents that take actions autonomously, with the OpenClaw ecosystem of agents touching customer records, financial systems, and the open internet" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: Then came **Claws** — a shorthand for the new breed of agents that don't just answer, they *act*. A Claw will chat with you and then go update the record, send the email, and make the payment, all on your behalf, turning read access into **write access**. And it's not one tool — the **OpenClaw ecosystem** shown here means every employee now has an army of agents touching customer records, financial systems, and the open internet, each one carrying that person's identity and permissions. That's a profound shift: the blast radius of a single mistake or a single manipulated agent is suddenly enormous. So how does this actually land inside a typical company? Let's follow the approval trail.

---

<!-- chrome: false -->

<img src="assets/slide-10.webp" alt="Agents got approved, security found out last — leadership decides, teams plug them in, security hears about it last; the choice between allow it or block it, and the real job is neither" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: Here's the uncomfortable pattern in most organizations today: **agents got approved, and security found out last**. Leadership decides on the timeline, teams plug agents into repos and cloud accounts and production data, and only then does security hear about it — usually once the agent already has access. That leaves security two bad options: **allow it** and take on risk nobody fully understands, or **block it** and get routed around as adoption moves into the shadows anyway. The real job, as the slide says, is **neither** — it's to help the business move fast *without* losing control. That reframing is the heart of governance, and it starts with understanding the one structural weakness every agent shares.

---

<!-- chrome: false -->

<img src="assets/slide-11.webp" alt="The lethal trifecta — access to private data, exposure to untrusted content, and ability to act externally; a useful agent has all three by design and the only fix is enforcement at runtime" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: This is the single most important concept in the whole session: the **lethal trifecta**. Every genuinely useful agent has three things at once — **access to private data**, **exposure to untrusted content** like web pages, emails, and MCP responses, and the **ability to act externally** by sending email or hitting APIs. Individually each is fine; together they mean untrusted content can carry an instruction the agent follows, using your private data, to take an action you never intended. And here's the kicker: a useful agent has **all three by design** — you can't train it out, prompt it out, or policy-doc it out. The only real fix is an **enforcement layer at runtime**, which is exactly what this workshop builds. Before we get there, let's see *why* this is so different from how we've always worked.

---

<!-- chrome: false -->

<img src="assets/slide-12.webp" alt="The Traditional Workflow — inner loop (code, open source, build, test) and outer loop (integrate, test, deploy) driven by humans at every stage; attack surface is only what you choose to pull" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: To feel the shift, look at how we've always shipped software. In the **traditional workflow**, a human sits at every stage — code, open source, build, test in the **inner loop**, then integrate, test, and deploy in the **outer loop** — and a person writes, reviews, and approves each step. The critical point is at the top: your **attack surface is only what *you* choose to pull**. A human deliberately decides which dependency, which base image, which change goes forward, so there's judgment at every gate. Hold that picture in your mind, because on the next slide we swap every one of those humans out.

---

<!-- chrome: false -->

<img src="assets/slide-13.webp" alt="The Agentic Workflow — same inner and outer loops but an agent sits at every stage instead of a human; the attack surface is no longer just what you pull" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: Now here's the **agentic workflow** — same inner and outer loops, but an **agent sits at every stage** where a human used to. Code, open source, build, test, integrate, deploy: each gate that used to have human judgment now has an autonomous actor exposed to untrusted content. And the line at the top is the whole warning of this section — the **attack surface is no longer just what you pull**; it's everything any agent at any stage might be induced to do. This is precisely why advice and training aren't enough, and why we need enforcement built into the platform. To ground all of this, let's get concrete about what agents are *already* doing inside your pipeline right now.

---

<!-- chrome: false -->

<img src="assets/slide-14.webp" alt="What agents are already doing in your pipeline — pulling dependencies, generating Dockerfiles, editing infrastructure like Terraform and Helm, and triggering builds and PRs on their own initiative" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: Let's make this real with four things agents are **already doing in your pipeline** today, not someday. They're **pulling dependencies** — resolving and adding packages you never chose, from registries you never audited. They're **generating Dockerfiles**, picking base images, install steps, and users. They're **editing infrastructure** — Terraform, Helm values, Compose files, even IAM policy documents. And they're **triggering builds**, opening PRs and kicking off pipelines on their own initiative. Every one of these touches the trifecta we just discussed, which is why our very first hands-on example is an agent containerizing our Product Catalog app — and why the next part of the workshop is about making these controls **enforced, not merely advised**.

---

<!-- chrome: false -->

<img src="assets/slide-15.webp" alt="A Product Catalog Sample App: architecture diagram showing an Application connected to PostgreSQL (product data), AWS S3 (product images), and Kafka (product updates) inside the Catalog Service, calling out to an Inventory service and other external services." width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: This is the **Product Catalog** app we'll thread through the whole workshop, so let me orient you. The **Application** at the center writes **product data** to **PostgreSQL**, pushes **product images** to **AWS S3**, and emits **product updates** onto **Kafka**, while also reaching out to an external **Inventory service** and other downstream services. It's deliberately realistic: a database, object storage, a message bus, and outbound calls that touch credentials and external systems. That surface area is exactly what makes it interesting to hand to an agent, because every one of those arrows is something an agent could touch, break, or leak. Keep this picture in mind, because next we ask the uncomfortable question: what can go wrong when an agent gets loose in here?

---

<!-- chrome: false -->

<img src="assets/slide-16.webp" alt="Full-screen title slide reading 'What can go wrong?' in dark navy on a light blue background with the Docker whale logo." width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: **What can go wrong?** I want to sit on this question for a moment, because it's the honest heart of the whole session. We just looked at an app with databases, storage, a message bus, and live credentials, and now we're about to give an autonomous agent the keys to work inside it. The failure modes aren't hypothetical; they're the everyday reality teams are already hitting. So over the next couple of slides we'll make it concrete and try it ourselves. Let's start with the most common task people hand an agent: containerising the app.

---

<!-- chrome: false -->

<img src="assets/slide-17.webp" alt="'Let's try' hands-on slide with a green gradient panel and a blue checkmark item: 'Agent containerising the Product Catalog application.'" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: Here's our first **hands-on** moment: we'll ask an agent to **containerise the Product Catalog application**. This is the bread-and-butter automation everyone wants, "write me a Dockerfile, get this running in a container", and it feels completely safe. What we're really testing is what an agent does when it's given a real app and no guardrails. Watch closely as it makes choices on your behalf, because those choices are where the risk hides. Next we'll see one specific way this goes sideways: the credentials.

---

<!-- chrome: false -->

<img src="assets/slide-18.webp" alt="'Let's try' hands-on slide with a green gradient panel and a blue checkmark item: 'Coding agents exposing the API and credentials.'" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: Now the sharper edge: **coding agents exposing the API and credentials**. To wire up that Postgres connection or the S3 upload, an agent will happily read your environment, bake secrets into a Dockerfile, echo a token into a log, or commit a `.env` it found lying around. It isn't malicious, it's just optimizing to make the task succeed, and a leaked credential is a perfectly good way to make a connection work. This is the trifecta problem in miniature: capability plus access plus no boundary equals exposure. Let me show you the anatomy of exactly how this happens on an unguarded host.

---

<!-- chrome: false -->

<img src="assets/slide-19.webp" alt="'Agent on a host system' diagram: inside 'Your host' (host daemon, host credentials, no boundary), a Prompt to containerize flows to an Agent with full permissions, which picks 'FROM node:20 chosen with no guidance' and reaches open registries with no allowlist, producing an image with 0 Critical, 6 High, 30 Medium, 54 Low CVEs, 431 packages, no SBOM, no attestation, root user." width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: This is what "no guardrails" actually looks like, drawn out. The agent runs **on your host** with the **host daemon**, **host credentials, and no boundary**; it has **full permissions**, pulls from **open registries with no allowlist**, and picks **`FROM node:20` with no guidance** because nobody told it to do better. The result is the box at the bottom: an image carrying **6 High and 30 Medium CVEs across 431 packages**, with **no SBOM, no attestation, and running as root**. Every one of those is a decision the agent made unsupervised, and every one is a place a security team would say no. So we're left with a real dilemma, which is the tension I want to name next.

---

<!-- chrome: false -->

<img src="assets/slide-20.webp" alt="'Speed or safety? This is the tension.' Two cards: Speed — 'Need autonomy but can't let agents run wild' (agents delete work, secrets exposed, systems damaged, one bad action real consequences); Safety — 'Lock everything down' (approve every file read, tool call, action; safe but defeats the purpose)." width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: This is **the tension every engineering leader is facing**. On the **Speed** side, you need autonomy, but an agent running wild can **delete months of work, expose secrets, and damage critical systems**, one bad action with real consequences. On the **Safety** side, you can lock everything down and **approve every file read, every tool call, every action**, but then you've just hired a very expensive assistant you have to babysit, and you've defeated the whole purpose. Most teams feel forced to pick a corner, and both corners are bad. Hold that frustration for a second, because the next slide asks the obvious question.

---

<!-- chrome: false -->

<img src="assets/slide-21.webp" alt="Full-screen title slide reading 'Why not get both?' in navy and blue on a light teal background with the Docker whale logo." width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: **Why not get both?** This is the whole thesis of the workshop in four words. The false choice between speed and safety only exists when your only control is a human clicking "approve", but that's not the only kind of control available. If the boundaries are **enforced by the environment** rather than by your attention, the agent can move fast *because* it's contained, not despite it. That's the shift from advice to enforcement, and it's exactly what Docker's approach delivers. Let me introduce the stack that makes "both" possible.

---

<!-- chrome: false -->

<img src="assets/slide-22.webp" alt="Full-screen dark navy title slide with the Docker logo reading 'Introducing AI Governance Stack.'" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: So let me introduce the **AI Governance Stack**, Docker's answer to that "both" question. Everything up to now has been the problem; from here on it's the solution, built as layers you can actually turn on. The key idea is that governance isn't a policy document or a training course, it's a set of **enforced mechanisms** wired into the same tools your developers already use. No new console to babysit, no separate platform to adopt. Let me show you what that looks like from a single control plane.

---

<!-- chrome: false -->

<img src="assets/slide-23.webp" alt="'AI Governance: One console to govern AI Agents and Claws across your entire organisation.' Bulleted benefits: one engine with three layers of control (sandbox, network, MCP), complete audit trails, centralized credential management across MCP servers, automated policy enforcement, governance for every stakeholder, and runtime PII redaction." width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: This is the promise stated plainly: **one console to govern AI agents across your entire organisation**. One engine gives you **three layers of control**, sandbox, network, and MCP, so you're governing isolation, egress, and tool access from the same place. Around that you get the things a security team actually asks for: **complete audit trails, centralized credential management across every MCP server, automated policy enforcement, and runtime PII redaction** before data ever reaches an agent or a log. And crucially, **policy propagates automatically** from security leaders down to every developer, so nobody has to remember to be safe. Now let me break that single engine into its layers so you can see how it's built.

---

<!-- chrome: false -->

<img src="assets/slide-24.webp" alt="'A layered approach to AI governance' table under 'Docker Business + AI Governance Policies': Gordon (in-product governance guidance in Docker Desktop), Agentic Compose & Docker Agent (local multi-agent orchestration via golden templates), Docker Model Runner (local air-gapped LLM execution), MCP Toolkit & Gateway (access only to authorized MCP servers), Docker Sandboxes (isolated portable runtime), and Docker Hardened Images (the trusted foundation)." width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: Here's the **layered approach**, the whole stack on one slide, and the important line at the top is that **the policies travel with the workload**, whether it runs on a laptop or in the cloud. At the base is **Docker Hardened Images**, the trusted foundation; above it **Docker Sandboxes** give an isolated, portable runtime; the **MCP Toolkit and Gateway** restrict the agent to only the servers your org authorizes; **Model Runner** keeps LLM execution local and air-gapped; **Agentic Compose** orchestrates multi-agent setups from golden templates; and **Gordon** delivers in-product guidance right inside Docker Desktop. Each layer adds one enforceable control, and they stack, so defense in depth rather than a single gate. We'll build up from the bottom, starting with that foundation layer.

---

<!-- chrome: false -->

<img src="assets/slide-25.webp" alt="'Layer 1 of 6 — Docker Hardened Images (DHI): The trusted foundation.' Bullets: agents pull images and libraries from Docker Hub that the organization scanned, verified, and authorized; minimal hardened base images shrink the attack surface; a known-good software supply chain established before a single agent runs. Includes a shield icon." width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: **Layer 1 of 6: Docker Hardened Images**, the trusted foundation the whole stack rests on. The idea is simple but powerful: agents only pull images and libraries that your **organization has already scanned, verified, and authorized**, so the raw material is known-good before any work begins. Because these are **minimal, hardened base images**, they strip out the packages an agent doesn't need, and that directly shrinks the attack surface, remember those 431 packages and 54 CVEs from the unguarded example. The point is that you establish a clean **software supply chain before a single agent runs**, rather than trying to clean up CVEs afterward. Let me show you what these images actually give you.

---

<!-- chrome: false -->

<img src="assets/slide-26.webp" alt="Docker Hardened Images marketing slide: 'Ultra-minimal footprint with near-zero CVEs', '7-day remediation for Critical and High CVEs, SLA-guaranteed', 'Built-in provenance, SLSA compliance, SBOMs', alongside a screenshot of the Docker Hardened Images catalog on Docker Hub (hub.docker.com/hardened-images/catalog)." width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: Here's what **Docker Hardened Images** actually deliver, and it's the screenshot of the real **catalog on Docker Hub** on the right so you can see this is a live product, not a concept. Three things matter most: an **ultra-minimal footprint with near-zero CVEs**, a **7-day remediation SLA on Critical and High CVEs** so someone is contractually on the hook to fix them, and **built-in provenance with SLSA compliance and SBOMs** so you can prove where every image came from. That last part is exactly the "no SBOM, no attestation" gap we saw earlier, closed by default. This is the audit evidence your security team wants, baked into the base layer. Enough slides, let's prove it, we'll migrate our app and compare.

---

<!-- chrome: false -->

<img src="assets/slide-27.webp" alt="'Let's try' hands-on slide with a green gradient panel and two blue checkmark items: 'Migrate the Node app to DHI' and 'Compare the images.'" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: Time for hands-on again: we'll **migrate the Node app to a Docker Hardened Image** and then **compare the two images** side by side. This is the payoff for the last few slides, we're going to take that CVE-laden `node:20` build and swap the base for a hardened one. Watch what happens to the numbers: the package count, the CVE count, the image size, and whether it still runs as root. Seeing the before-and-after is what makes this real, so we don't have to take the claims on faith. Once we've locked down the foundation, the next layer is about containing what the agent can actually do while it runs.

---

<!-- chrome: false -->

<img src="assets/slide-28.webp" alt="Layer 2 of 6: Docker Sandboxes — isolated, portable, policy-bound runtime; every agent runs in a sandbox with explicit endpoint and read/write limits, workloads move between laptop and cloud, and enforced policies travel with the sandbox." width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: This is **Layer 2** of our six-layer stack, and it's the foundation everything else sits on: **Docker Sandboxes**, an isolated, portable, policy-bound runtime. The core idea is that every agent runs inside a **sandbox** with explicit limits on what endpoints it can reach and what it can read or write — nothing is open by default. Because these sandboxes are portable, you can start on a developer laptop and move the exact same workload to the cloud and back without changing the rules. The critical property is that **enforced policies travel with the sandbox**, so the guardrails follow the agent wherever it runs rather than being reconfigured per environment. With the agent safely contained, the next question is what it's allowed to reach — and that's the gateway layer.

---

<!-- chrome: false -->

<img src="assets/slide-29.webp" alt="Layer 3 of 6: MCP Toolkit & Gateway — controlled tool access; locks down what agents and sandboxes can reach, permits connections only to organization-authorized MCP servers, and provides a single governed gateway for every agent's tool calls." width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: **Layer 3** is the **MCP Toolkit & Gateway**, which governs controlled tool access — the tools and external services an agent is allowed to call. Where the sandbox contains the agent, this layer locks down what those agents and sandboxes are actually permitted to reach on the outside. It permits connections only to **MCP servers the organization has authorized**, so an agent can't wander off to some arbitrary or malicious tool endpoint. Everything funnels through a **single, governed gateway** for every agent's tool calls, which means one place to set policy and one place to audit. Once tools are governed, the next concern is where the model itself runs — which brings us to running inference locally.

---

<!-- chrome: false -->

<img src="assets/slide-30.webp" alt="Layer 4 of 6: Docker Model Runner — local, air-gapped LLM execution; run large language models locally alongside the workload, keep inference inside the organization's boundary for air-gapped environments, and remove dependence on external model endpoints." width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: **Layer 4** is the **Docker Model Runner**, which handles local, air-gapped LLM execution. Instead of every prompt leaving your environment to hit a third-party API, you can run large language models **locally, right alongside the workload**. That keeps inference **inside the organization's boundary**, which is exactly what regulated or air-gapped environments need — the data and the model never cross the perimeter. It also **removes dependence on external model endpoints**, so you're not exposed to an outage, a price change, or a data-handling policy you don't control. With compute, tools, and models all governed, the next layer is about making that governance the default when teams spin up new agents.

---

<!-- chrome: false -->

<img src="assets/slide-31.webp" alt="Layer 5 of 6: Agentic Compose & Docker Agent — secure golden templates; declarative multi-agent orchestration with security parameters baked in, reusable golden templates for spinning up new agents and sandboxes, so governance becomes the default starting point." width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: **Layer 5** is **Agentic Compose and the Docker Agent**, which give you **secure golden templates**. This is declarative, multi-agent orchestration with the **security parameters baked right in** — the same Compose-style approach developers already know, extended to agents. The payoff is **reusable golden templates** for spinning up new agents and sandboxes, so a team doesn't hand-roll isolation and policy every time. The key cultural shift is that **governance becomes the default starting point, not an afterthought** — you inherit the guardrails instead of remembering to add them. The final layer makes all of this approachable, with guidance built right into the product.

---

<!-- chrome: false -->

<img src="assets/slide-32.webp" alt="Layer 6 of 6: Gordon — in-product governance guidance; the AI assistant built directly into Docker Desktop, answers governance and implementation questions in context, and guides teams toward best practices as they build." width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: **Layer 6**, the top of the stack, is **Gordon** — in-product governance guidance. Gordon is the **AI assistant built directly into Docker Desktop**, so help lives where the work happens rather than in a wiki someone forgot to read. It answers **governance and implementation questions in context**, meaning a developer can ask "how do I lock this down?" right at the moment they're building it. That's how it **guides teams toward best practices as they build**, turning policy from a document into an in-the-moment nudge. Now that we've walked all six layers individually, let's step back and see how they fit together as one system.

---

<!-- chrome: false -->

<img src="assets/slide-33.webp" alt="The Docker AI governance ecosystem — a system diagram showing all six layers working together on laptop or cloud: Docker Business plus AI governance policies, trusted supply chain with Docker Hub and Hardened Images, agents each in their own Docker Sandbox, Model Runner for local inference, MCP Toolkit & Gateway to authorized MCP servers, and Gordon for guidance." width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: Here's **the whole ecosystem on one slide** — all six layers working together as a single system, on the laptop or in the cloud, under **Docker Business plus AI governance policies**. On the left, the **trusted supply chain** feeds in **Docker Hardened Images** — scanned, verified, and with authorized images and libraries — so what the agents build from is clean. In the middle, **each agent runs in its own Docker Sandbox**, backed by the golden templates and the local **Model Runner** for air-gapped inference. On the right, the **MCP Toolkit & Gateway** routes tool calls only to **authorized MCP servers**, while **Gordon** provides guidance across the top. The single most important line here is at the bottom: this runs on any enterprise laptop or in the cloud and back, and **enforced policies travel with every sandbox** — which is exactly the promise we opened with. Let me distill that into the security properties your team actually cares about.

---

<!-- chrome: false -->

<img src="assets/slide-34.webp" alt="Keeping your system safe with multiple layers of security — four cards: Kernel level isolation (microVMs give separate kernel space for untrusted AI agent workloads), Rootless by default (microVMs run without privileged access, host compromise prevented even with container escape), Network (allowlist-based egress filtering at the VM boundary, block-by-default, secrets not exposed to agent), and MCP Servers (private catalog, servers run isolated, secrets engine protects secrets from server)." width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: This slide is the one to show your **security team**, because it names the concrete mechanisms — four of them. **Kernel-level isolation** means each agent gets its own **microVM with separate kernel space**, so an untrusted agent workload isn't sharing a kernel with your host. **Rootless by default** is the crucial follow-on: the microVMs run without privileged access, so even a full **container escape doesn't hand over the host**. On the **network** side, egress is **allowlist-based and block-by-default at the VM boundary**, and **secrets are never exposed to the agent** directly. And for **MCP servers**, there's a **private catalog**, the servers run isolated, and a **secrets engine keeps credentials away from the server** itself. These are hard, enforced controls — not advice — and next we drop into the hands-on portion where we sandbox a real agent.

---

<!-- chrome: false -->

<img src="assets/slide-35.webp" alt="Section title slide: Sandboxing the agent, on a dark blue Docker-branded swirl background with the Docker logo." width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: This is our **section break into the hands-on portion** — everything up to now has been the why and the what, and from here we get our hands dirty **sandboxing the agent**. In the next few minutes we'll take the running **Product Catalog** example and actually put an agent inside a sandbox, then watch how isolation, secrets, network, and filesystem rules constrain what it can touch. The goal is for you to see the guardrails behave, not just hear about them. Let's start with what a sandbox actually is and why you'd run an agent in one.

---

<!-- chrome: false -->

<img src="assets/slide-36.webp" alt="Sandboxes (Experimental) — Run agents in isolation rather than on your bare machine; the agent or Claw runs in an isolated sandbox mirroring your workspace, you define its access to filesystem, network, internal resources and tools, and your real files, data, and secrets stay safe even if the agent goes off rails; with a terminal showing 'sbx run claude' starting a sandboxed Claude agent with a deny-all network policy." width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: Here's the concrete tool: **Docker Sandboxes**, still marked **Experimental**, which run agents in isolation **rather than on your bare machine**. On the right you can see it in action — `sbx run claude` spins up a Claude agent in a sandbox that mirrors your workspace, and notice the **network policy: deny all, allow 42 hostnames**, so it's locked down from the first second. The three ideas on the left are the whole pitch: the **agent runs in an isolated sandbox** that mirrors your workspace, **you define exactly what access it gets** to filesystem, network, internal resources, and tools, and your **real files, data, and secrets stay safe even if the agent goes off the rails**. That last point is the reason this exists — you get the productivity of an autonomous agent without betting your machine on it. Let's look under the hood at how that isolation is actually built.

---

<!-- chrome: false -->

<img src="assets/slide-37.webp" alt="Sandbox architecture diagram — inside a Host machine, a microVM-based sandbox holds a container running the Agent or Claw; Workspace directories mount into it, while Network policies and Secrets feed a Network proxy that mediates all connections to External systems." width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: This is the **architecture** behind that command, and it's worth tracing the arrows. On the **host machine**, the agent — or Claw — runs inside a **container, which itself sits inside a microVM-based sandbox**; that's the two-layer boundary we talked about. Your **workspace directories** mount into the sandbox so the agent can do real work, but notice that **network policies and secrets don't go to the agent** — they feed a separate **network proxy**. Every connection to **external systems** has to pass through that proxy, which is where the allowlist is enforced and where credentials are injected without the agent ever seeing them. So the agent gets to work but never holds the keys or picks its own destinations — the host stays in control. Next let's see how this generalizes across the different agents your teams might use.

---

<!-- chrome: false -->

<img src="assets/slide-38.webp" alt="Docker Sandbox (for code-executing agents) — when an agent reads, writes, and executes code, containers aren't enough, so the Sandbox adds a hard hypervisor boundary; a microVM AI Coding Agent connects through Docker AI Governance components (Filesystem Manager to source dir, Network Proxy to Anthropic/GitHub, MCP Gateway to a Notion MCP Server) with a list of supported agents including Claude Code, Codex, Copilot, Cursor, Gemini, Droid, Kiro, OpenCode, and Docker Agent." width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: This slide makes the **why** explicit: when an agent **reads, writes, and executes code, a plain container isn't enough**, so the Sandbox adds a **hard hypervisor boundary** via the microVM. Trace the path — the **AI Coding Agent** in its microVM reaches the outside world only through **Docker AI Governance**: a **Filesystem Manager** mediates access to the source directory, a **Network Proxy** governs egress to services like Anthropic or GitHub, and an **MCP Gateway** brokers tool calls to something like a Notion MCP server. On the right, notice the **breadth of supported agents** — Claude Code, Codex, Copilot, Cursor, Gemini, and more — so this isn't tied to one vendor's agent. The bottom line says it best: the coding agent runs in a microVM, and **every external action goes through Docker AI Governance**. That naturally raises the question — why a microVM and not just a container, or a VM, or WASM? — which the next slide answers directly.

---

<!-- chrome: false -->

<img src="assets/slide-39.webp" alt="A new gold standard for safe isolation — comparison table rating sbx against a full VM, container-based approaches (e.g. Modal), WASM-based (e.g. Cloudflare), and bare (no sandboxing) across depth of isolation/safety, speed of spin-up/tear-down, and ability to let agents use Docker containers; sbx scores full kernel isolation, near-instant spin-up/tear-down, and supports Docker containers within the sandbox." width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: This is the **comparison that justifies the microVM choice**, laid out against the real alternatives: a full VM, container-based approaches like Modal, WASM-based approaches like Cloudflare, and bare with no sandboxing at all. Read it across three rows — on **depth of isolation**, `sbx` gets **full kernel isolation**, where containers aren't strong boundaries and bare is wide open. On **speed**, it delivers **near-instant spin-up and tear-down**, avoiding the slow boot of a full VM while still being safe. And critically, on the third row, it can **run Docker containers inside the sandbox** — something a full VM does awkwardly and WASM basically can't. The message is that you no longer have to trade safety for speed or for compatibility; `sbx` gives you all three, which is why we call it a **new gold standard**. With the theory settled, let's actually run the demos.

---

<!-- chrome: false -->

<img src="assets/slide-40.webp" alt="Demos — four cards previewing the hands-on exercises: Basic walkthrough, Credential isolation, Network isolation, and Filesystem isolation." width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: Here's the **roadmap for the hands-on demos**, and it maps directly onto the security properties we just discussed. We'll start with a **basic walkthrough** to get a sandbox running, then work through the three isolation dimensions in turn: **credential isolation**, where the agent works without ever holding the secrets; **network isolation**, where egress is blocked by default and allowlisted; and **filesystem isolation**, where the agent only touches what you've explicitly mounted. Each demo takes one of the abstract guardrails and lets you watch it actually stop something. Let's open the first exercise and put our **Product Catalog** agent into a sandbox.

---

<!-- chrome: false -->

<img src="assets/slide-41.webp" alt="Try sbx CLI: pick up your Docker Hub Org, set up the lab working directory, and look at the policy model" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: This is our jump-off point into the first hands-on block with the **sbx CLI**, the tool that actually enforces the sandboxing we've been talking about. Three things to get sorted before we type anything real: pick up your **Docker Hub Org** so your secrets and policies are scoped correctly, set up your **lab working directory** as the workspace the agent will mount, and take a first look at the **policy model** that governs what the agent may touch. I want you to see that governance here isn't a slide deck concept, it's a CLI you run. Get these three set up and you're ready to launch an agent under guardrails. Let's start with the install.

---

<!-- chrome: false -->

<img src="assets/slide-42.webp" alt="Install. Sign up. Go. No Docker Desktop required. brew install docker/tap/sbx and winget install Docker.sbx, with a terminal showing sbx run claude starting a sandboxed Claude agent" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: The friction here is deliberately near zero: **install, sign up, go**, and note the callout, **no Docker Desktop required**. One line on macOS with `brew install docker/tap/sbx`, one line on Windows with `winget install Docker.sbx`, and you're running. The terminal shows exactly what launch looks like: `sbx run claude` starts a Claude agent inside a sandbox, mounts just your workspace, and prints the **network policy** right up front, deny all, allow a named set of hostnames. That last line sums up the whole workshop, do something incredible, but with proper **agent guardrails**. Now let's open the hood and see what that sandbox boundary is actually made of.

---

<!-- chrome: false -->

<img src="assets/slide-43.webp" alt="Diagram: Sandbox VM on the host system with a hypervisor boundary; agent process, private Docker engine, installed packages and VM filesystems are blocked from host filesystem, host processes, host Docker engine and host network; a proxy applies allow/deny policies and credential injection, allowing api.github.com and blocking bad.exfil.example" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: This is the mental model I want everyone to hold onto. The agent runs inside a **Sandbox VM** sitting behind a **hypervisor boundary**, not just a container namespace but a real VM barrier. Look at the red arrows: the agent process, its private Docker engine, its packages and filesystems are all walled off from your **host filesystem, host processes, host Docker engine, and host network**. The only way out is through the **proxy**, which applies allow/deny policies and injects credentials, so `api.github.com` gets through while `bad.exfil.example` is dropped. Every hands-on section that follows is really just configuring one edge of this diagram. We'll start with the most sensitive edge of all, credentials.

---

<!-- chrome: false -->

<img src="assets/slide-44.webp" alt="Section title slide: Docker logo with heading Credential Isolation on a dark blue background" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: Here's the section that tends to change minds in the room, **Credential Isolation**. This is the question your security team will ask first: if an agent needs my Anthropic key, my GitHub token, my cloud credentials to do its job, doesn't handing those over defeat the entire point of sandboxing it? The answer is no, and the mechanism is genuinely elegant. The agent gets to *use* your credentials without ever being able to *read* them. Let me show you exactly how that works.

---

<!-- chrome: false -->

<img src="assets/slide-45.webp" alt="Credential isolation diagram: OS keychain (sbx secret set -g, encrypted at rest) and AI Agent in a microVM sandbox both feed a forward proxy on the host, which injects the Authorization header and forwards an authenticated request to the model/API provider; note that credential values never enter the sandbox VM" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: Follow the flow. Your keys live in the **OS keychain**, encrypted at rest, put there with `sbx secret set -g`. When the agent makes an API call, it sends the request with **no key in the VM**, the request hits the **forward proxy running on the host**, and the proxy does the key lookup and injects the `Authorization` header before forwarding an authenticated request to the model or API provider. The agent gets a working, authenticated call; it never sees the secret. That's the green banner at the bottom and the whole point, **credential values never enter the sandbox VM**. So even a fully compromised or prompt-injected agent has nothing to exfiltrate. Let's look at the commands you'd actually run to manage these.

---

<!-- chrome: false -->

<img src="assets/slide-46.webp" alt="Managing credentials: Always prefer stored secrets over env vars because keychain encrypts at rest; Never set API keys manually inside the sandbox where the agent can read them. CLI examples for sbx secret set global and scoped, piping gh auth token, and sbx secret ls/rm; supported services include anthropic, openai, github, google, groq, mistral, xai, aws, nebius" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: Two rules to live by, and they're color-coded for a reason. **Always** prefer stored secrets over environment variables, because the keychain encrypts at rest, and **never** set API keys manually inside the sandbox, because anything you set in there is readable by the agent, which throws away the isolation we just built. The CLI is small: `sbx secret set -g anthropic` stores a key globally for all sandboxes, dropping the `-g` scopes it to one sandbox, and you can pipe `gh auth token` straight in so the agent uses the `gh` CLI without ever holding the raw token. And the supported list is broad, **anthropic, openai, github, google, groq, mistral, xai, aws, nebius**, so most real toolchains are covered. Before we run it live, let me show you the difference this makes in plain before-and-after terms.

---

<!-- chrome: false -->

<img src="assets/slide-47.webp" alt="Side-by-side comparison: Without Sandbox, full environment and credential access exposes ANTHROPIC_API_KEY, GITHUB_TOKEN, and STRIPE_API_KEY to the agent; With Sandbox, credentials are injected, IS_SANDBOX is set, no keys found, yet the agent still communicates with the model" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: This is the proof in a single screen. On the left, **without a sandbox**, we ask the agent about its configured dev tools and it happily enumerates them, reading `ANTHROPIC_API_KEY`, `GITHUB_TOKEN`, even a `STRIPE_API_KEY` straight out of its environment, that's your entire secret store sitting one prompt away from exposure. On the right, **with the sandbox**, the same probe comes up empty: `IS_SANDBOX` is set, grepping for keys finds nothing, and yet the agent notes it's *obviously* still talking to the model. That gap, fully functional but zero readable secrets, is credential isolation working exactly as designed. Now let's actually run it and watch that happen live.

---

<!-- chrome: false -->

<img src="assets/slide-48.webp" alt="Demo section slide: Demo on the left, checklist item Credential Isolation on the right" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: Time to prove it rather than assert it, this is our **Credential Isolation demo**. I'll store a key in the keychain, launch the agent in a sandbox, and then try to get the agent to reveal it, and you'll watch it fail to find anything while still making a successful authenticated call. Keep an eye on that distinction as I go: the request works, the secret stays invisible. Follow along in your own terminal if you're set up. Once we've contained *what the agent knows*, the next question is *where the agent can reach*, so we move to the network.

---

<!-- chrome: false -->

<img src="assets/slide-49.webp" alt="Section title slide: Docker logo with heading Network Isolation on a dark blue background" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: Next layer of the sandbox, **Network Isolation**. Credential isolation stops the agent from reading your secrets; network isolation stops it from *phoning home* to somewhere it shouldn't, whether that's an attacker's exfiltration endpoint or just an unvetted package registry. This is your defense against the classic exfiltration and data-leak scenarios, and it's the mechanism that makes prompt injection far less dangerous, because even a hijacked agent can only talk to destinations you've explicitly allowed. Let me show you how the network boundary is enforced.

---

<!-- chrome: false -->

<img src="assets/slide-50.webp" alt="Network isolation diagram: AI Agent in a microVM sandbox on an isolated network with no host localhost, raw TCP/UDP/ICMP/DNS blocked at the network layer; all HTTP/S goes through a network proxy that enforces allow-list domains and DNS via proxy, permitting api.anthropic.com and api.github.com while blocking everything else including private IPs, loopback, link-local and unlisted destinations" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: Same shape as the credential diagram, which is the point, it's one consistent model. The agent sits on an **isolated network with no host localhost**, and at the network layer **raw TCP, UDP, ICMP, and DNS are simply blocked**, so it can't open arbitrary sockets or do sneaky DNS tricks. Every bit of traffic is forced through the **network proxy** as HTTP/S, and the proxy enforces an **allow-list of domains** with DNS resolved on its side. So `api.anthropic.com` and `api.github.com` go green, and **everything else**, private IPs, loopback, link-local, anything unlisted, gets dropped. The footer says it plainly: only allow-listed domains are reachable, everything else dies at the proxy. Let's see how you configure that allow-list.

---

<!-- chrome: false -->

<img src="assets/slide-51.webp" alt="Managing network: Every sandbox has its own isolated network; no shared network between a sandbox and your host. CLI examples for sbx policy ls, allow network for all sandboxes or a specific sandbox, and deny network with a wildcard; blocked by default list includes raw TCP/UDP/ICMP, private IP ranges, loopback, link-local, host filesystem, host Docker daemon and inter-sandbox traffic" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: Two principles up top: **every** sandbox gets its own isolated network, and there's **no shared network** between a sandbox and your host, so nothing leaks laterally. The CLI mirrors the secrets one: `sbx policy ls --type network` shows what's active, `sbx policy allow network api.example.com` opens a domain for everyone, adding `--sandbox` scopes it to one, and `sbx policy deny network "*.ads-tracker.io"` blocks with wildcards. The right-hand panel is the important part for your security team, it's the **deny-by-default list**: raw TCP/UDP/ICMP, private IP ranges, loopback, link-local, host filesystem, the host Docker daemon, and even inter-sandbox traffic. You're not building a blocklist and hoping you caught everything; you're opening a small, explicit allow-list on top of a locked-down default. Let me show you the difference on a real prompt-injection attempt.

---

<!-- chrome: false -->

<img src="assets/slide-52.webp" alt="Side-by-side comparison: Without Sandbox, full network access lets a prompt injection in a GitHub issue exfiltrate data to evil.example.com; With Sandbox, controlled network access returns a 403 Forbidden blocked by network policy, and the agent flags it as a close call" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: This is the scenario that keeps security teams up at night, made concrete. We ask the agent to help fix a GitHub issue, and that issue contains a **prompt injection** telling the agent to POST some data off to `evil.example.com`. On the left, **without a sandbox**, the agent has full network access, follows the malicious instruction, and the exfiltration succeeds, game over. On the right, **with the sandbox**, the agent tries the exact same call and the proxy returns **403 Forbidden, blocked by network policy**, the agent even flags it as a close call. Notice the agent still got *tricked*, the injection worked, but the network boundary meant it couldn't do any harm. That's defense in depth: you don't have to make the agent perfectly un-foolable, you just have to contain what a fooled agent can reach. Let's run this one live.

---

<!-- chrome: false -->

<img src="assets/slide-53.webp" alt="Demo section slide: Demo on the left, checklist item Network Isolation on the right with the link https://dockerworkshop.vercel.app/ai-governance/network-demo/" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: Now the **Network Isolation demo**, and there's a link on screen, `dockerworkshop.vercel.app/ai-governance/network-demo`, so you can follow the interactive walkthrough on your own machine. I'll set an allow-list, run the agent against that injected GitHub issue, and we'll watch the exfiltration attempt hit a hard `403` at the proxy instead of succeeding. Try changing the policy yourself and see what gets through and what doesn't, that hands-on feel is exactly the point. Once we've locked down credentials and network, the last thing to govern is the *tools* the agent calls, which brings us to protecting MCP.

---

<!-- chrome: false -->

<img src="assets/slide-54.webp" alt="Section title slide: 'Filesystem Isolation' with Docker logo on a dark navy waved background." width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: This is the third pillar of sandboxing the agent: **filesystem isolation**. We've already contained the agent's identity and its network reach; now we tackle what it can *read and write* on disk. This is where a lot of the real-world fear lives, because an over-eager agent cleaning up "old logs" can just as easily delete your source tree or exfiltrate your SSH keys. Let me show you exactly how the sandbox draws that boundary so the agent only ever sees what you deliberately hand it.

---

<!-- chrome: false -->

<img src="assets/slide-55.webp" alt="Diagram: 'Filesystem isolation' — host filesystem shares only the Workspace dir to an AI Agent container as /workspace (bind-mounted); ~/.ssh, /etc, keys and rest of host are 'never mounted'. microVM-based sandbox with its own kernel & filesystem." width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: Here's the mental model: on the host, **only the workspace directory is shared** with the agent, bind-mounted in as `/workspace`. Everything else, your `~/.ssh`, `/etc`, credentials, the rest of the host, is **never mounted**, so it simply doesn't exist from the agent's point of view. The agent runs **non-root** inside a **microVM-based sandbox with its own kernel and filesystem**, and crucially, **symlinks pointing outside the workspace are not followed**, so it can't trick its way out. And because the VM is disposable, `sbx rm` deletes the VM and its filesystem entirely. This is the enforced-not-advised principle applied to disk: the boundary is a real mount namespace, not a polite instruction.

---

<!-- chrome: false -->

<img src="assets/slide-56.webp" alt="Two-terminal comparison: left 'Without Sandbox - Full Filesystem Access' shows an agent deleting the whole host Videos/build directories with rm -rf; right 'With Sandbox - Controlled Filesystem Access' shows the agent seeing only a single project directory and cleaning safely." width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: This side-by-side makes it visceral. On the left, **without a sandbox**, the same "clean up old logs" prompt leads the agent to scan the entire home directory, find the biggest folders, and `rm -rf` your Videos and build directories, files that can't be recovered. On the right, **with the sandbox**, the agent runs the identical reasoning but only ever sees a single project directory, so the worst it can do is tidy `project/.logs` and hand back a cheerful "happy to have helped." Same model, same prompt, wildly different blast radius, and the only thing that changed is the boundary around it. That's the whole argument for isolation in one screen.

---

<!-- chrome: false -->

<img src="assets/slide-57.webp" alt="Demo divider slide: large 'Demo' on a green gradient panel; checklist on the right with 'Filesystem Isolation' checked." width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: Let's move from slides to a live **demo** and actually watch filesystem isolation in action. I'll drop into a sandbox, try to reach outside the workspace, and show you that the host stays untouched no matter what the agent attempts. Follow along in your own terminal if you'd like, this is the last of the sandboxing pillars, so afterward we'll have covered isolation, secrets, network, and filesystem end to end. Then we'll zoom back out to the commands that tie it all together.

---

<!-- chrome: false -->

<img src="assets/slide-58.webp" alt="Two-column cheat sheet 'Key CLI Commands' listing sbx commands: run claude, ls, stop, rm, exec -it, ports --publish, policy allow network, policy log, secret set -g anthropic, and bare sbx for the TUI, each with a plain-language description." width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: Here's your **cheat sheet**, the whole sandbox workflow in ten commands. `sbx run claude` starts or reconnects a sandbox for the current directory; `sbx ls` shows status, uptime, and ports; `stop` pauses while preserving state and `rm` deletes everything inside. The governance-relevant ones are in the middle: `sbx policy allow network` opens a domain, `sbx policy log` shows what was blocked or allowed, and `sbx secret set -g anthropic` tucks an API key into your OS keychain so it never touches disk in the sandbox. Keep this slide handy, everything we've demoed maps to one of these lines. And notice you can also just type `sbx` on its own to open a visual dashboard, which is exactly what's next.

---

<!-- chrome: false -->

<img src="assets/slide-59.webp" alt="Screenshot of 'The Sandbox TUI': Docker Sandboxes terminal dashboard listing sandboxes (claude-docs running, claude-agent-demo stopped) alongside a Network Log showing 10 allowed and 1 blocked outbound connections with hosts, hits, and status." width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: This is the **Sandbox TUI**, the single pane of glass for everything we've discussed. On the left you see your sandboxes with their state, running or stopped, and on the right a live **network log** showing exactly which hosts the agent reached, with a running tally of allowed versus blocked connections. Notice the one **blocked** entry in red, that's a policy decision captured in real time, right next to the allowed traffic. This is the "one console" promise from the start of the workshop made concrete: presence, policy, and audit in one view. It's a natural handoff point, because now we shift from sandboxing the agent to governing the **tools** it calls.

---

<!-- chrome: false -->

<img src="assets/slide-60.webp" alt="Section title slide: 'Protecting MCP' with subtitle 'Tools are how agents act on the world. Govern which servers exist and which tools an agent may actually call at one gateway.' Docker logo on dark navy background." width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: We've locked down the agent's environment; now we govern its **reach into the world**. **Tools are how agents actually act**, calling APIs, reading repos, searching the web, and MCP is the protocol that exposes those tools. The problem is that an ungoverned agent can wire up any server it likes, so the governance question becomes twofold: **which servers even exist**, and **which specific tools an agent may call**. The answer in this section is to funnel all of it through **one gateway** where policy and audit apply. Let me show you what that looks like architecturally.

---

<!-- chrome: false -->

<img src="assets/slide-61.webp" alt="Diagram 'The agent talks to one gateway, never to servers directly': Agent (in sandbox) connects via SBX_MCP_URL to a single mcp-gateway, which aggregates local-wiki, GitHub/Notion, and DuckDuckGo behind it. Callout: every tool call flows through one chokepoint; tools namespaced mcp__mcp-gateway__<tool>." width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: Here's the core pattern: **the agent talks to one gateway, never to servers directly**. It connects through a single value, `SBX_MCP_URL`, to the **mcp-gateway**, and every downstream server, your local wiki, GitHub, Notion, DuckDuckGo, is **aggregated behind that one endpoint**. Because every tool call flows through this single **chokepoint**, that's the exact place where policy and audit apply, and the tools are **namespaced** as `mcp__mcp-gateway__<tool>` so you always know what's being invoked. The agent never holds a dozen server URLs and credentials, it holds one. That single-endpoint design is what makes the next question, "point it where?", so important.

---

<!-- chrome: false -->

<img src="assets/slide-62.webp" alt="Slide 'Point it at a real gateway, nothing else works': two options — Local gateway (http://localhost:8811, Compose/Desktop MCP Toolkit, best for learning) and Hosted control plane (https://gateway.docker.com, MCP Gateway Enterprise, org policy + central audit). Callout: fail-closed by design — no policy loaded means deny-all; policy authored in Docker Hub, fetched at docker login, developers can't override." width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: `SBX_MCP_URL` is the gatekeeper: the whole `sbx mcp` subtree **stays hidden until this value is set**, so a misconfigured agent gets no tools at all. You have two places to point it. A **local gateway** at `localhost:8811`, run via Compose or the Desktop MCP Toolkit, is best for learning the mechanics because you control what's registered. The **hosted control plane** at `gateway.docker.com` is the real governance story: org policy decides what's invocable, with central audit. And critically, it's **fail-closed by design**, no policy loaded means deny-all, the policy is authored centrally in Docker Hub, fetched at `docker login`, and developers **can't override it**. That precedence rule is exactly what we'll trace end to end next.

---

<!-- chrome: false -->

<img src="assets/slide-63.webp" alt="Architecture diagram 'Author once in Docker Hub, enforce around the sandbox and at the gateway': Docker Hub AI Governance (Settings UI + Governance API) synced at docker login (takes precedence, fail-closed) to the host developer laptop, where a MicroVM agent goes through the sbx daemon (network proxy + policy, filesystem policy) and an Audit log (every decision, JSONL), and out to the MCP Gateway (local 8811 or gateway.docker.com) with policy check + audit on every call." width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: This diagram ties the entire workshop together. Policy is **authored once in Docker Hub** under AI Governance, then **synced to every laptop at `docker login`**, where it **takes precedence** and is **fail-closed**. On the host, the **sbx daemon** enforces the same policy engine around the sandbox, network proxy and filesystem policy wrapping the MicroVM agent, while every decision is written to an **audit log** as JSONL. That same authored policy then rides out to the **MCP Gateway**, whether local on 8811 or hosted, where there's a **policy check and audit on every call**. So it's one policy, authored centrally, enforced in two places, around the sandbox and at the gateway, with a complete record of every decision. Now let's get hands-on with how you actually register and attach these servers.

---

<!-- chrome: false -->

<img src="assets/slide-64.webp" alt="Hands-on 'The server lifecycle - five commands': (1) add — sbx mcp add local-wiki with --command docker and --args; (2) ls/inspect the record; (3) --static-mcp to attach to a sandbox via sbx run claude --static-mcp local-wiki or sbx mcp load; (4) /mcp verifies inside the agent showing mcp-gateway connected with 24 tools." width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: Here's the **server lifecycle** in a handful of commands. Step one, **`add`** registers a server, here a local wiki backed by a Docker image, and it supports lists, stdio, remote-OAuth, or a `docker.io` image. Step two, **`ls` / `inspect`** shows the record, and note this is **registration only, it doesn't start the server**. Step three, you **`--static-mcp`** to attach it to a sandbox at launch, or `sbx mcp load` into a live one, and watch the flag, it's `--static-mcp`, not `--mcp`. Step four, inside the agent you type **`/mcp`** to verify, and you'll see **one aggregated gateway, not your individual servers**, here reporting 24 tools connected. Register, inspect, attach, verify, that's the loop, and now let me show it with a hardened server.

---

<!-- chrome: false -->

<img src="assets/slide-65.webp" alt="Hands-on 'The server lifecycle - five commands (Using DHI MCP Server)': (1) add — sbx mcp add remotedhi --url https://dhi.io/mcp; (2) ls/inspect remotedhi; (3) --static-mcp attach via sbx run claude --static-mcp remotedhi or sbx mcp load remotedhi; (4) /mcp shows mcp-gateway connected with 9 tools." width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: Same five-command lifecycle, now with the **DHI MCP server**, the hardened tools that complete our Product Catalog story. We **`add remotedhi`** by URL, `inspect` the record, `--static-mcp` it onto the sandbox, and verify with `/mcp`. Notice the result: the gateway now reports **9 tools**, a deliberately tight, curated surface rather than a sprawling one. This is the same governed flow you just learned, pointed at a **hardened, Docker-authored MCP server**, so the tools your agent can reach are as trustworthy as the DHI base images we started the day with. The obvious next question is: who decides which of those 9 tools an agent may actually call? That's the policy layer.

---

<!-- chrome: false -->

<img src="assets/slide-66.webp" alt="Slide 'Default-deny allow-list over (server, tool), authored in Cedar': a Cedar policy from app.docker.com permits action invokeTool when resource.server == 'remotedhi' and the tool is in a list of read-only dhi_get_* / dhi_list_* tools. Right panel 'What this permits': anyone in the org may register servers; read-only DHI tools are invocable; evaluated at the gateway on every invoke; same engine as network + filesystem policy. Callout: author once, sync everywhere — sbx mcp add any server, but non-permitted tool calls are denied and audited." width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: This is the payoff, a **default-deny allow-list over (server, tool) pairs, authored in Cedar**. Read the policy: it permits `invokeTool` only when the server is `remotedhi` **and** the tool is one of a specific set of **read-only** DHI operations, `dhi_get_image_cves`, `dhi_list_repositories`, and so on. So even though **anyone in the org may register servers**, only those read-only tools are actually invocable, the policy is **evaluated at the gateway on every invoke**, and it's the **same engine** that drives our network and filesystem policies, one surface, no bypass. The rule is **author once, sync everywhere**: a developer can `sbx mcp add` any server they like, but if org policy doesn't permit its tools, the calls are **denied and audited**. That's the complete governance loop, and it sets up our final section on audit, scale, and proof for your security team.

---

<!-- chrome: false -->

<img src="assets/slide-67.webp" alt="One feature: agents in a sandbox, using external tools safely — an AI agent in a Docker sandbox reaches MCP servers (Notion, Stripe, GitHub, Browser) through one governed gateway, answering four questions: what tool-servers are supported, how to register them, how the sandbox connects (static vs dynamic), and how credentials are managed" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: This slide frames the entire **Protecting MCP** story as one feature: an agent inside a sandbox reaching external tools like **Notion, Stripe, GitHub, or a browser through a single governed gateway**. I want you to hold onto the four practical questions on the bottom half, because they map exactly to what a security team will ask you: **what tool-servers are supported**, **how you register them**, **how the sandbox connects** — as a locked static set or a discoverable dynamic catalog — and **how credentials are managed**, with the gateway holding OAuth centrally so you can authorize, revoke, and audit. The key phrase is "through one governed gateway": the agent never talks to Stripe or GitHub directly, it goes through a chokepoint we control. That's the whole scope of this piece, and everything else we've built — isolation, network, filesystem — is what makes that gateway trustworthy. Now let's see how you actually turn this on inside Docker Hub.

---

<!-- chrome: false -->

<img src="assets/slide-68.webp" alt="Section title slide: Setting up AI Governance under Docker Hub, with the Docker logo on a dark navy wave background" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: This is our transition into the **audit and scale** portion — moving from concepts to the actual console your organization administers. Everything we've discussed so far, isolation, network policy, filesystem rules, MCP control, all of it lives under one roof: **AI Governance in Docker Hub**. I'm going to walk you through the exact six steps an org owner takes, from opening the dashboard to reading the audit logs, so you can reproduce this the moment you get back to your desk. The point of this section is proof: your security team wants to see the enforcement surface, not just hear about it. Let's start at step one, the front door.

---

<!-- chrome: false -->

<img src="assets/slide-69.webp" alt="Step 1: Open https://app.docker.com — screenshot of the Docker AI Platform console showing Manage AI governance for the whalecollab organization, with an AI governance toggle and a left nav listing Manage, Network access, Filesystem access, MCP access, and Audit logs" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: Step one is simply opening **app.docker.com** and going to your organization's **AI Platform** area — here you can see the org is "whalecollab." I want you to notice the left-hand navigation, because it's essentially a table of contents for this whole workshop: **Manage, Network access, Filesystem access, MCP access, and Audit logs**. Each of those is one of the guardrails we've been building, now surfaced as a menu item an admin can click. The **Manage AI governance** panel is where you monitor and control AI usage across the entire organization from one place. This is the "one console" promise made concrete — so let's flip the master switch that activates it.

---

<!-- chrome: false -->

<img src="assets/slide-70.webp" alt="Step 2: Enable AI Governance — close-up of the Manage AI governance panel with the AI governance toggle switched on, noting that audit logs are stored in Docker Cloud by default, capturing member actions, timestamps, and IPs" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: Step two is a single toggle, but it's the one that matters most: **enabling AI governance** turns on tracking, policy enforcement, and activity review for the whole org. Read the fine print with me — the moment this is on, **audit logs are stored in Docker Cloud by default, capturing member actions, timestamps, and IPs**. That's your accountability trail, on from the very first day, without any extra setup. This is the difference between advice and enforcement we talked about earlier: nothing here is a suggestion an engineer can ignore, it's an org-level control that applies to everyone. With governance switched on, the next three steps are where we actually define what agents are allowed to do — starting with the network.

---

<!-- chrome: false -->

<img src="assets/slide-71.webp" alt="Step 3: Configure Network Access — Network access policy list showing URLs coding agents can reach through the sandbox, with organization-scoped policies like denycollabnix, allowanthropic, denypaste, allowdocker, allowpy, allowdmr, and allowhf, each marked as allow or deny" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: Step three is **network access**, and this is where the sandbox stops being a black box and becomes a policy surface: these are the **URLs coding agents can reach through the sandbox**. Notice the mix of **allow and deny** rules, all scoped to the organization — `allowanthropic` and `allowdocker` let the agent reach the endpoints it legitimately needs, while `denypaste` and `denycollabnix` block egress paths you don't trust. This is default-deny thinking in practice: the agent can only talk to what you've explicitly permitted, so a compromised or confused agent can't exfiltrate data to some random host. Every one of these is a named, dated, auditable policy, not a firewall rule buried in infrastructure. Once you've decided where an agent can go on the network, the next question is what it can touch on disk.

---

<!-- chrome: false -->

<img src="assets/slide-72.webp" alt="Step 4: Add Network Access Rule — Filesystem access policy list showing local paths coding agents can mount to, with organization-scoped allow policies allowwork, workdemo, labproject, and allowcodexx" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: Step four moves to **filesystem access** — the **local paths coding agents are allowed to mount into the sandbox**. Just like the network rules, these are explicit allow policies: `allowwork`, `workdemo`, `labproject`, `allowcodexx` — each one grants the agent a specific directory and nothing else. This is how you stop an agent from wandering into your SSH keys, your home directory, or another project's source: it simply can't mount what isn't on this list. Combined with the network policies from the last slide, you've now bounded the agent on two axes — where it can reach and what it can see. Next we govern the most powerful axis of all: the tools it can invoke through MCP.

---

<!-- chrome: false -->

<img src="assets/slide-73.webp" alt="Step 5: Configure MCP Access — MCP access page showing Cedar policies that control MCP tool access for the organization, listing demo-github-readonly, allowikimcp, and allowalltools with their permit statements referencing MCP::Action::invoke and MCP::Action::register" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: Step five is the heart of the MCP story: **Cedar policies that control which MCP tools an agent can access** across the organization. Look at the policy language — these are real `permit` statements written in **Cedar**, gating actions like `MCP::Action::invoke` and `MCP::Action::register`. A policy like `demo-github-readonly` means an agent can call GitHub tools but only read, never write; `allowalltools` is the wide-open counterpart you'd use in a trusted context. This is the governed gateway from slide 67 made real: the agent asks to invoke a tool, and Cedar decides yes or no before anything runs. It's fine-grained, declarative, and centrally managed — exactly what a security reviewer wants to see. The last step is proving all of this actually happened, which brings us to the audit trail.

---

<!-- chrome: false -->

<img src="assets/slide-74.webp" alt="Step 6: Auditing and Logging — Audit Logs page reviewing policy enforcement events across the organization, showing counts for Events, Allowed, Denied, and Consent required, an event-type filter dropdown (Tool Invocation, Network Egress, Filesystem Mount, Policy Action, etc.), and Policy Action rows for principal ajeetraina777" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: Step six closes the loop: the **Audit Logs** page, where you **review every policy enforcement event across the organization**. The summary tiles up top — **Events, Allowed, Denied, and Consent required** — give your security team an at-a-glance posture, and the event-type filter lets you drill into exactly the category they care about: **Tool Invocation, Network Egress, Filesystem Mount, Policy Action**, and more. Every row ties an action back to a **principal** — here, a real username — with timestamps and decisions. This is the artifact you hand to compliance: not "we think the agent behaved," but a queryable, exportable record of every decision the guardrails made. That completes the six-step setup, so let me point you to where you can read more.

---

<!-- chrome: false -->

<img src="assets/slide-75.webp" alt="References slide listing the link https://www.docker.com/products/ai-governance/" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: Everything we just walked through — the console, the policies, the audit logs — is documented at the one link on this slide: **docker.com/products/ai-governance**. I'd encourage you to bookmark it, because it's the canonical starting point when you go to set this up for your own organization. If you take one URL away from today, this is the one. Now, before we wrap, I want to briefly show how you extend the sandbox itself with new capabilities.

---

<!-- chrome: false -->

<img src="assets/slide-76.webp" alt="Section title slide: Adding Tools to Sandbox, with the Docker logo on a dark navy wave background" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: This short section answers a question I always get at this point: "The governance is great, but how do I actually add real capabilities to the sandbox?" So far we've focused on constraining the agent; now let's look at extending it — giving it memory, models, and tools — while keeping every one of those guardrails intact. The theme here is that you can make an agent genuinely powerful without punching holes in the isolation. Let me show you the architecture with a concrete example.

---

<!-- chrome: false -->

<img src="assets/slide-77.webp" alt="Adding tools to Sandbox via sbx kits — architecture diagram of a host machine containing a microVM-based sandbox with an Agent/Claude container, a Mem0 memory layer (Kind: mixin), a Qdrant local store, and Docker Model Runner on the host running ai/gemma3 (LLM) and ai/mxbai-embed-large (embedder), with network policies and secrets feeding a Network Policy box before any External Systems; note: the memory loop stays on the host, nothing crosses to external systems" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: This diagram shows how you add tools with **sbx kits**, and I love it because it proves governance and capability aren't in tension. Inside the **microVM-based sandbox**, the agent gets a real **Mem0 memory layer** — delivered as a "mixin" kit — backed by a **Qdrant local store**, and it uses **Docker Model Runner on the host** to run `gemma3` for the LLM and `mxbai-embed-large` for embeddings. Here's the crucial part, in blue on the right: **the entire memory loop stays on the host — nothing crosses to external systems**, and any traffic that would leave still passes through the **Network Policy** box we configured earlier, fed by our secrets and network rules. So the agent gets persistent memory and local models, a genuinely capable setup, yet the data never leaves your machine and every egress is still governed. That's the whole thesis of this workshop in one picture, which is the perfect note to close on.

---

<!-- chrome: false -->

<img src="assets/slide-78.webp" alt="Closing brand slide: Docker logo with the tagline Run agents freely. Safely. on a dark dotted background" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: This is the whole message of the day in four words: **run agents freely, safely**. Everything we did — sandboxing the agent, hardened images collapsing CVEs, network and filesystem and credential isolation, the governed MCP gateway, and the audit trail — exists so your teams can give agents real autonomy without giving up control. "Freely" is the productivity your developers want; "safely" is the assurance your security team needs; the point is you no longer have to choose between them. That's what enforced, layered governance buys you. Let me leave you with a genuine thank you and open it up for questions.

---

<!-- chrome: false -->

<img src="assets/slide-79.webp" alt="Closing slide: large Thank You. text centered on a dark navy wave background with the Docker logo" width="1600" height="900" loading="lazy" decoding="async" style="position:absolute;inset:0;width:100%;height:100%;max-width:none;max-height:none;object-fit:fill" />

Note: Thank you — genuinely — for spending this time working through AI governance with us. We covered the full arc today: why autonomous agents need guardrails, why those guardrails must be **enforced rather than merely advised**, the **layered Docker stack** that delivers them, and the hands-on work of **sandboxing the agent and protecting MCP**, all the way through to **audit and scale** in one console. My ask is simple: pick one thing from today — enabling AI governance, writing a single network policy, or a Cedar rule for MCP — and try it this week, because the console is right there at docker.com. I'll hang around for **Q&A**, so bring me your hardest questions about your own environment. Thank you again, from the Docker and Redington teams.
