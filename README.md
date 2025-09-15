Employment–Social Protection (SP) Interoperability Standards

A Digital Convergence Initiative (DCI) project

📖 Overview

This repository hosts the draft interoperability standards for connecting Public Employment Services (PES) with Social Protection (SP) systems.

It is part of the Digital Convergence Initiative (DCI), launched under USP2030 to accelerate universal social protection through digital transformation and system interoperability.

The goal:

Transform social protection from a safety net into a springboard to employment and economic independence.

Provide governments with practical, implementable standards for integrating PES and SP systems.

Create a shared, version-controlled GitBook documentation and GitHub-hosted technical specs.

🎯 Objectives

Define common processes, data standards, and APIs for Employment–SP integration.

Document evidence-based use cases (referral, compliance monitoring, status verification, employment subsidies).

Provide country examples (Chile, South Korea, Turkey, South Africa, Brazil, etc.) to ground standards in real-world practice.

Deliver:

Standards documentation (GitBook)

Data schemas and API specifications (GitHub)

A demo showcasing interoperability use cases

Webinar and presentation materials

🏗️ Repository Structure
README.md                # Project introduction (this file)
SUMMARY.md               # GitBook navigation

/common/                 # Reusable DCI standards (COM scope)
  assumptions.md
  processes.md
  data-objects.md
  code-directories.md
  data-types-formats.md
  design-principles.md

/employment-interface/   # Employment–SP specific standards (EMPL scope)
  process/
    use-cases.md
    workflows.md
    assumptions-business-logic.md
  data/
    data-objects.md
    code-directories.md
    data-types-formats.md
    data-governance.md
  api/
    design-principles.md
    api-methods.md
    message-structure.md
    error-security.md

/implementation/         # Adoption & guidance
  dci-integration.md
  country-examples.md
  governance-legal.md
  operational-guidance.md
  monitoring-evaluation.md

/annexes/                # Supporting material
  previous-versions.md
  openapi-specs.md
  jsonld-examples.md
  glossary.md

CLAUDE.md                # AI assistant instructions for repo maintenance

🧩 Core Components
1. Process Standards (PRS.EMPL.xx)

Beneficiary referral to PES

Employment status verification

Conditionality & compliance monitoring

Subsidy integration with SP registries

2. Data Standards (DO.EMPL.xx, CD.EMPL.xx)

Data objects: Person, Referral, Benefit, Participation

Code directories: referral reasons, subsidy types, employment status codes

Formats: JSON-LD with common context definitions

Governance: privacy, minimization, retention aligned with law

3. API Standards (API.EMPL.xx)

RESTful patterns with OAuth2/JWT

Methods: Search, Notify, Subscribe, Status

Message structure with standard headers and signatures

OpenAPI specs in /annexes/openapi-specs.md

🌍 Evidence Base

Standards are grounded in:

Chile: Social Registry (RSH) + Employment subsidies (SEJ, SMT); Unemployment Insurance + BNE/OMIL

South Korea: KEIS + Work24 integration, NBLSS & NES

Turkey: İŞKUR + Social Assistance referral

South Africa: Employment status verification with SASSA–DoL

Other pilots and good practices from Brazil, Uruguay, Kosovo, Philippines

📅 Deliverables & Timeline

As per ToR and revised inception report:

Sept 2025 – Research synthesis & draft standards

Oct 2025 – Revised draft + API documentation + Demo

Nov 2025 – Final standards, webinar & presentation

🔒 Principles

Interoperability layers: legal, governance, organizational, semantic, technical

Data minimization & protection: Only share what’s necessary; align with local data protection laws

Common vs specific: Reuse COM standards where possible; only diverge for Employment-specific needs.

Versioning: GitBook space titles (e.g. Employment v1.0.0) + API versioning in design principles.

🤝 Contributing

Branching: Use feature branches (feat/use-case-x, docs/api-refactor).

Commits: Follow Conventional Commits
.

Pull requests: One logical change per PR. Checklist:

 Correct labels (PRS.EMPL.01, DO.COM.03, etc.)

 Valid JSON/YAML examples

 Links resolve across repo

 Evidence cited or marked TODO(evidence)

📚 References

DCI Standards: standards.spdci.org

Global Accelerator for Jobs & Social Protection

Talking Interoperability series