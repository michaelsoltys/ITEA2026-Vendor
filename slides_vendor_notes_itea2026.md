# Vendor Lock-In Slides — Speaker Notes & Q&A Reference

Companion notes for `slides_vendor_itea2026.md` (ITEA TIW 2026, Track 6, Siena Room, Wed Apr 29, 2:30–3:00 PM).

These notes provide deeper context for terms and concepts in the slides — useful for fielding audience questions or refreshing memory mid-talk.

For logistics/correspondence (Jim Alich, deadlines, file submissions), see `notes.md` in this directory.

---

## ONNX (Open Neural Network Exchange)

**What it is:** A vendor-neutral, open-source format for representing trained machine learning models. Originally created by Facebook and Microsoft in 2017; now governed by the Linux Foundation.

**Why it matters (slide 9):** Without ONNX, a model trained in PyTorch on AWS SageMaker is locked to that ecosystem. With ONNX, you export the model once, then run it on any inference engine — TensorRT, ONNX Runtime, Triton, CoreML, OpenVINO. 1,700+ tools support the format.

**What's actually in an ONNX file:**
1. **Computation graph** — how layers connect, in what order
2. **Weights** — the trained numerical parameters
3. **Operator definitions** — framework-neutral descriptions of math operations
4. **Metadata** — input/output shapes, data types, version info

A model file *without* its framework context is like a Word document without Microsoft Office: the data is there but instructions for interpreting it are vendor-specific. ONNX captures both structure AND weights in an open format any compliant runtime can execute.

**Bidirectional:** PyTorch → ONNX → can convert back via `onnx2torch`. True interchange standard, not just an archive format.

**Limits to mention if asked:**
- Custom operators may not have ONNX equivalents (proprietary layers compiled as code)
- Performance can vary across runtimes — same model may be 30% faster on TensorRT than on ONNX Runtime
- Cutting-edge architectures (latest LLM techniques) may not have ONNX support yet — there's typically a 6–12 month lag

**Q&A-ready punch line:**
> "ONNX is to ML models what HTML is to web pages — a portable format that any compliant tool can render, regardless of who made it."

---

## OCI Containers (Open Container Initiative)

**What it is:** An open standard under the Linux Foundation that defines how container images are built and run. *Not* the same as Docker — Docker is one OCI-compliant tool among many.

**Why it matters (slide 9):** The slide says "OCI-compliant containers + Kubernetes" rather than "Docker + Kubernetes" because OCI compliance means your workloads run on:
- Docker
- Podman (Red Hat's daemonless alternative)
- containerd (used by Kubernetes itself)
- CRI-O (Kubernetes-native runtime)

You're not tied to Docker Inc. as a vendor or to its specific tooling. Same image works across all four.

**The history:** Docker created the container format in 2013, then donated the spec to OCI in 2015 to prevent Docker (the company) from controlling the standard. This was deliberate avoidance of vendor lock-in — directly relevant to your talk's thesis.

**Q&A-ready punch line:**
> "OCI compliance is what lets you build a container once and run it on any container runtime, on any cloud, on bare metal in an air-gapped enclave. That portability is what makes the whole technical strategy work."

---

## Kubernetes / Kubeflow

**Kubernetes:** Google open-sourced its internal "Borg" system as Kubernetes in 2014, then donated it to the Cloud Native Computing Foundation (CNCF). Why it matters here: AWS, Azure, and Google all offer managed Kubernetes (EKS, AKS, GKE), but because the core is CNCF-governed, your workloads are **portable across all three** without rewriting deployment manifests.

**Kubeflow:** ML-specific extension of Kubernetes. Provides standardized primitives for training jobs, hyperparameter tuning, model serving, and pipelines. The "vendor-neutral workflow" claim on slide 9 means: same Kubeflow pipeline runs on EKS today, AKS tomorrow, on-prem next year.

**Why this is the perfect lock-in counterexample:**
- Single vendor (Google) created the technology
- Voluntarily donated it to a neutral foundation
- Now used by all major clouds, with no vendor controlling the core
- This is the model the vendor lock-in talk is advocating for AI

---

## Terraform & Infrastructure-as-Code (IaC)

**What it is:** A declarative tool — written by HashiCorp — for defining cloud infrastructure in code. You write `.tf` files describing resources (VMs, networks, databases), and Terraform creates/updates them in any provider.

**Why it matters (slide 9):** A Terraform module that deploys an ML pipeline can target AWS, Azure, GCP, or on-prem with minimal code changes. The infrastructure is **diffable, reviewable, version-controlled** — and an AI coding assistant can refactor it from one provider to another in hours, not months.

**The license caveat:** HashiCorp moved Terraform to a Business Source License (BSL) in 2023, which prompted the open-source community to fork it as **OpenTofu** (Linux Foundation governed). Both work; serious DoW deployments are increasingly choosing OpenTofu specifically for vendor-neutrality.

**Alternatives if asked:**
- **Pulumi** — IaC in real programming languages (Python, TypeScript, Go) instead of HCL
- **AWS CloudFormation / Azure ARM** — vendor-specific (lock-in!) — counter-example
- **Crossplane** — Kubernetes-native infrastructure orchestration

---

## JWCC (Joint Warfighting Cloud Capability)

**What it is:** The DoW's $9 billion multi-cloud contract awarded December 2022 to four providers: **AWS, Azure, Google, and Oracle**. Replaces the cancelled JEDI single-vendor contract that was tied up in litigation.

**Why it matters (slide 9):** JWCC is *itself* an anti-lock-in policy decision. By awarding to four providers instead of one, the DoW forces multi-cloud-aware procurement and prevents any single vendor from controlling DoW cloud workloads.

**What you can run on it:** IL2 through IL6 (unclassified through SECRET) workloads. RAG systems, data lakes, ML training pipelines, tactical edge deployments.

**Where it lives in the talk:** Listed under "Infrastructure-as-Code" as a multi-vendor cloud option. The point: even at the contract level, the DoW has made vendor diversity a procurement requirement.

---

## CDAO GIDE (Global Information Dominance Experiments)

**What it is:** A series of operational experiments run by the Pentagon's Chief Digital and AI Office (CDAO) that demonstrates vendor-agnostic data integration at DoW scale.

**History:**
- Originally created by NORTHCOM in 2020
- Handed to CDAO in 2022
- Runs on **90-day cycles** — incremental test, measure, field on live operational networks
- Software engineers sit directly beside warfighters at combatant commands for real-time feedback
- **GIDE 9** (early 2024) was the first to demonstrate a completely vendor-agnostic data integration layer
- CDAO leveraged $100M in FY24-25 for GenAI pilots integrated into GIDE
- The AI Rapid Capabilities Cell (launched Dec 2024) now uses GIDE to test frontier AI models with warfighters

**The 99.5% availability claim (slide 12):** Translates to ~44 hours of downtime per year. Not extraordinary for enterprise systems — but the significance is that they achieved it ACROSS MULTIPLE VENDOR ENVIRONMENTS SIMULTANEOUSLY. The conventional argument against vendor-agnostic architectures is that abstraction layers degrade reliability. GIDE proved the opposite.

**Q&A-ready punch line:**
> "GIDE proved you can run across multiple vendors and still maintain availability within tactical decision-making requirements. The conventional wisdom — that abstraction layers degrade reliability — is wrong."

---

## Hegseth Acquisition Reforms (Nov 7, 2025)

**The speech:** Secretary of War Pete Hegseth, National War College, November 7, 2025. Made vendor lock-in avoidance a **mandatory structural requirement** in DoW acquisition reform — not just best practice.

**Four core contractor requirements:**
1. **Accept risk and invest in capacity** — vendors share program risk, not just deliver outputs
2. **Embrace commercial "85% solutions"** — buy what works now rather than custom-build to perfection
3. **Supply chain resilience** ← *this is the dual-source mandate* — "maintain at least two qualified sources for critical program content"
4. **Performance-based contracting** — pay for outcomes (accuracy, availability), not platforms

**Structural changes:**
- **Portfolio Acquisition Executives (PAEs)** replace Program Executive Offices (PEOs)
- PAE compensation is **tied to delivery speed and competition** — financial incentive against single-source dependence
- "Speed to delivery" mentioned 25+ times in the speech as the organizing principle
- Wartime Production Unit with deal teams and financial incentives

**Why this slide is structurally important:** The whole talk's thesis — "do this technical thing to enable lock-in avoidance" — would be optional advice without this slide. With it, the technical strategies become *what the policy now demands*.

**Q&A-ready answer if asked "is this enforceable?":**
> "Yes. PAE compensation is tied to delivery speed and competition outcomes — not vendor relationships. The dual-source requirement is a contractor obligation, not a recommendation. We're past the era where single-source AI awards were debatable."

---

## PAEs vs PEOs

**PEOs (Program Executive Offices):** Traditional structure. Each major program has its own PEO managing acquisition. Tendency over time: incumbent vendors become entrenched, switching costs grow, and PEO success metrics don't penalize lock-in.

**PAEs (Portfolio Acquisition Executives):** New structure under Hegseth reforms. Manage *portfolios* of related programs, not individual programs. Compensation tied to delivery speed and competition health, not just program outcomes.

**Why the change matters:** PAEs have institutional incentive to maintain vendor diversity *across* their portfolio. A single locked-in vendor in their portfolio threatens their compensation.

---

## SBOM (Software Bill of Materials)

**What it is:** A formal, machine-readable inventory of every software component in a deliverable — open-source libraries, dependencies, versions, licenses. Standard formats: **SPDX**, **CycloneDX**.

**Why it's on slide 10:** Required by Executive Order 14028 (Biden, May 2021) and reinforced by NIST SP 800-218. For AI deliverables, SBOMs cover not just code but model weights, training data lineage, and inference framework versions.

**Why it enables lock-in avoidance:** Without an SBOM you don't know what you're depending on. With one, you can identify which components have alternatives and which are vendor-controlled. It's the bill of materials for your software supply chain.

---

## Background IP vs Foreground IP

**Background IP:** Intellectual property the contractor brings to the project — pre-existing software, frameworks, model architectures, trade secrets. The contractor retains ownership.

**Foreground IP:** Intellectual property created during the contract — custom training data, fine-tuned models, integration code, deliverables. The government's data rights to this matter enormously.

**Why slide 10 distinguishes them:** Without explicit contract language, defense contractors routinely classify everything as background IP, leaving the government with limited rights. The slide says: explicitly require **unlimited rights to foreground IP** — training data, model weights, source code created under the contract. Otherwise vendor lock-in is built into the IP terms before you've written a line of code.

---

## Modular Contracting / Software Acquisition Pathway

**Modular contracting (slide 10):** Breaking a large AI project into small, interoperable increments where multiple vendors can compete for each module. Different vendors can win different modules — preventing single-vendor monopolization of the program.

**Software Acquisition Pathway (SWP):** A DoW acquisition track specifically designed for iterative software delivery. Created in 2020 under DoDI 5000.87. Bypasses the slow waterfall acquisition process. Encourages modular delivery.

**Why this is anti-lock-in:** Traditional cost-plus contracts reward vendors for complexity. Modular fixed-price contracts force interoperability — because each module has to plug into modules from other vendors. Forced interoperability is anti-lock-in by construction.

---

## Apache Iceberg

**What it is:** An open table format for huge analytic datasets. Originally built at Netflix; now governed by the Apache Software Foundation. Used by Snowflake, Databricks, AWS Athena, Google BigLake, Starburst.

**Why it matters (slide 9):** Iceberg sits between your data and any query engine. You can store the data once and query it from Spark, Trino, Flink, DuckDB — without moving the data or rewriting it. Vendor-neutral data lakes.

**Compare to vendor-locked alternative:** AWS proprietary Glue catalog with Parquet-only storage. Works great in AWS, painful to migrate. Iceberg solves this by keeping the table format itself open.

---

## MLflow

**What it is:** Open-source platform for managing the ML lifecycle — experiment tracking, model registry, deployment. Originally Databricks; now Linux Foundation governed.

**Why it matters (slide 9):** Without MLflow (or equivalent), each vendor's ML platform tracks experiments in its own format. Switching from SageMaker to Vertex AI means losing your experiment history. With MLflow, that metadata is portable.

**The 800+ contributors detail:** Proves it's a real community-governed project, not a single-vendor sponsored "open" project that's actually controlled by one company.

---

## The Vendor Lock-In Spectrum

This is the *concept* the slides build on without naming it explicitly. Worth understanding for Q&A.

**Lock-in is not binary — it's a spectrum.** Some choices have low lock-in cost, others have high cost. Examples:

| Service | Lock-in level | Why |
|---------|--------------|-----|
| Linux VMs (EC2, Azure VMs, GCE) | LOW | Generic compute. Easy to migrate. |
| S3 / Azure Blob / GCS | LOW–MED | Object storage APIs differ but conversion is straightforward |
| Managed Kubernetes (EKS/AKS/GKE) | LOW | CNCF-governed core. Workloads are portable |
| Managed databases (RDS, Cosmos, Cloud SQL) | MED | Some lock-in via proprietary features |
| Lambda / Azure Functions / Cloud Functions | HIGH | Serverless function format, event triggers, runtime — all vendor-specific |
| AWS Bedrock / Azure OpenAI Service / Vertex AI | HIGH | Each integrates differently with the rest of its ecosystem |
| Anthropic-only / OpenAI-only model APIs | EXTREME | Single point of failure (see: Feb 2026) |

**Stickiness analogy:** In web design "stickiness" means designing pages users return to. Managed cloud services are sticky in the *negative* sense — they're hard to leave. Slide 4 ("What Does Lock-In Look Like Today?") captures this point.

---

## DoW Open Source Software Memo (Jan 2022)

**What it is:** A formal DoD/DoW memorandum acknowledging that open-source software is *first-class* in defense — not a fallback or risk. The relevant passage is the slide 7 callout: *"Lock-in can occur even with open source through expertise dependencies and specialized implementations."*

**Why this nuance matters:** A naive reading of the talk is "use open source = avoid lock-in." That's wrong. You can be locked into open-source software too — through customizations, vendor-specific deployment patterns, or simply having only one engineer who understands the implementation.

**Q&A-ready answer if asked "doesn't open source solve everything?":**
> "No. Open source removes contract-level lock-in but not expertise-level lock-in. If only one vendor knows how your custom Kubernetes operators work, you're locked in even though Kubernetes is open. The DoW open-source memo from 2022 explicitly acknowledges this. Solving lock-in requires both technical AND procurement strategies."

---

## The Anthropic Incident (Feb–Mar 2026)

**Timeline:**
- **July 2025** — Claude approved for classified networks (first frontier model)
- **Feb 27, 2026** — President directed all federal agencies to cease using Anthropic
- **Mar 3, 2026** — DoW formally notified Anthropic of supply chain risk designation (first ever for an American company)
- **Mar 5, 2026** — Public designation
- **GSA removed** Anthropic from USAi.gov; **DoW** removed it from GenAI.mil; Navy commands began removing Claude from systems
- **Apr 8, 2026** — Court denied Anthropic's appeal to pause designation; litigation continues

**Why this is the talk's payoff example:**
- Programs that hard-coded Claude API calls into their RAG pipelines faced months of disruption
- Programs that used **abstraction layers** (LangChain, Haystack — slide 9 technologies) swapped to GPT-4o or Gemini by changing a model identifier
- This is **exactly** the architecture the talk is advocating

**Q&A-ready answer if asked "couldn't this happen to OpenAI or Google?":**
> "Yes — and that's the point. The lesson isn't 'avoid Anthropic.' The lesson is 'design so any single LLM provider is replaceable.' The Anthropic case is a stress test that revealed which programs were architected for vendor independence and which weren't. Next time it could be OpenAI for a different policy reason. Treat the LLM as a replaceable component."

---

## "85% Solutions"

**What it means (slide 8):** Hegseth's reform language for buying commercial off-the-shelf (COTS) capability that meets 85% of requirements *now* rather than custom-building to 100% over years.

**The acquisition philosophy:** A 100% custom solution that takes 5 years and is then 5 years out of date is worse than an 85% solution fielded in 6 months. Commercial AI moves so fast that "perfect" defense AI built from scratch is obsolete by deployment.

**Implication for vendor lock-in:** 85% solutions tend to be commercial cloud and commercial AI services — which means you're more likely to face lock-in pressure than with custom builds. The slide's point: this is *fine* IF you architect for switching. Modular contracting + open standards + IaC make 85% solutions safer because you can swap vendors when the next 85% solution arrives.

---

## CDAO Itself

**What it is:** **Chief Digital and AI Office** (CDAO) — the Pentagon office responsible for accelerating AI adoption across DoW. Headed by the Chief Digital and AI Officer (currently Dr. Radha Plumb).

**Mandate:** Advise the Secretary of Defense on data, analytics, and AI strategy; manage the JWCC contract; run GIDE; oversee GenAI.mil (covered in the RAG talk); lead the AI Rapid Capabilities Cell.

**Why CDAO comes up repeatedly in this talk:** GIDE is a CDAO program. JWCC is administered by CDAO. The frontier AI contracts ($200M each to Google, OpenAI, xAI) were CDAO awards. CDAO is the operational arm of Hegseth-era AI acquisition reform.
