---
name: arch-security-review
description: "Analyzes an uploaded architecture diagram (image/screenshot/exported drawing) and produces a security architecture review mapped against PCI DSS, PCI SSF, ISO 27001, NIST CSF, NIST 800-207 Zero Trust, CIS Controls v8, CSA CCM, OWASP ASVS/API Security Top 10, STRIDE, RBI cybersecurity/digital-payment regulations, and India's DPDP Act/Rules (privacy). Trigger whenever the user attaches/references an architecture, data-flow, or network diagram and asks for a security review, threat model, compliance gap analysis, or standards assessment (PCI/ISO/NIST/CIS/RBI/DPDP/privacy/cloud/zero-trust/API security) — even without naming a specific standard. Also trigger for reviewing a design before it's built, cardholder-data/payment/personal-data flows, segmentation/trust boundaries, or a bank/NBFC/payment-system/consumer-app design for Indian regulatory alignment."
---

# Architecture Diagram Security Review

## What this does

Takes one architecture diagram (a picture — PNG/JPG screenshot, exported draw.io/Lucidchart/Visio image, whiteboard photo, etc.) and turns it into a structured security review: an inventory of what's in the diagram, the trust boundaries and data flows, a STRIDE threat pass, and findings mapped against whichever of ten standards actually apply — PCI DSS, PCI SSF, ISO 27001, NIST CSF, NIST 800-207 (Zero Trust), CIS Controls v8, CSA CCM, OWASP ASVS/API Security Top 10, and RBI's cybersecurity/digital-payment regulations — each finding with a concrete recommendation.

## Why the process matters

A security architecture review is only useful if the reader can trust it. Two failure modes are equally bad:
- **Hallucinated confidence** — inventing a control that "must" be there because a well-architected system would have it, or inferring encryption/auth that isn't actually shown. This creates false assurance, which is worse than no review at all.
- **Noise** — flagging every theoretically possible issue regardless of what's actually visible, burying the findings that matter under boilerplate.

The fix for both is the same discipline: only claim what the diagram shows or explicitly labels, mark everything else as an open question, and prioritize findings by actual exposure (is this component internet-facing, does it touch cardholder/PII data, does it cross a trust boundary) rather than by how many standards a generic issue happens to map to.

## Step 1 — Read the diagram

Look at the image directly (no OCR needed for clean digital diagrams; if it's a messy whiteboard photo, work harder to make out labels and say so if something is illegible). Extract:

- **Components** — every box/node: apps, services, APIs, databases, queues, caches, load balancers, third-party integrations, external actors/users
- **Data flows** — every arrow: source, destination, and direction. Note any labels (protocol, port, data type, "TLS", "encrypted", auth method)
- **Trust boundaries** — anything the diagram itself indicates as a boundary: DMZ, VPC/subnet boxes, "internet" vs "internal", CDE (cardholder data environment) markers, cloud account boundaries, on-prem vs cloud
- **Data classification signals** — anything suggesting PAN/track data/cardholder data, PII, credentials/secrets, health data, or other regulated data, based only on labels actually present (a box labeled "Payments DB" or "Customer PII Store" counts; a generic "Database" box does not — don't assume)

Build this as an inventory table before doing anything else:

| # | Component | Type | Data handled (if labeled) | Exposure (internet-facing / internal / unknown) |

If a label is ambiguous or missing, write "not labeled" rather than guessing. If the diagram is too sparse to do a meaningful review (e.g., just 3 unlabeled boxes), say so plainly and ask the user for more detail or a legend instead of padding the output.

## Step 2 — Map trust boundaries and data flows

List each boundary crossing explicitly: `Component A → Component B, crosses [internet → DMZ], carries [data type], protocol/encryption: [as labeled, or "not shown"]`. This list is the backbone of the rest of the review — nearly every real finding in an architecture review is really a statement about a specific crossing, not about a component in isolation.

## Step 3 — STRIDE pass

For each component and each boundary-crossing flow, walk through: Spoofing, Tampering, Repudiation, Information Disclosure, Denial of Service, Elevation of Privilege. Only note a category if there's a concrete reason it applies given what's in the diagram (e.g., "no auth shown on the flow from the mobile client to the Orders API" is a real Spoofing note; "databases can theoretically be tampered with" is not — skip generic entries).

## Step 4 — Map findings to frameworks

Read the relevant reference file(s) based on what showed up in Step 1-3, then map each concrete finding to the applicable control(s). Don't load a reference file you don't need — ten frameworks loaded indiscriminately produces a bloated, repetitive review; the trigger conditions below exist precisely to keep each review scoped to what's actually relevant.

| Reference file | Load when... |
|---|---|
| `references/iso27001.md` | Always — general baseline for any system |
| `references/cis-controls.md` | Always — practical technical baseline that complements ISO's more abstract controls |
| `references/stride-owasp.md` | Always — turns the Step 3 STRIDE pass into ASVS-style architecture points |
| `references/zero-trust.md` | Always — the review's whole premise (trust boundaries, data flows) is Zero Trust's subject matter |
| `references/pci-dss.md` | Any component/flow labeled as handling cardholder data, PAN, or payment processing, or sitting inside/near a CDE-like boundary |
| `references/pci-ssf.md` | Only when the diagram shows a payment application's *internals* (sub-components, EMV kernel, tokenization logic) rather than treating payment as one opaque box |
| `references/rbi.md` | The entity is a bank/NBFC/payment aggregator/card-network participant in India, or payment data is in scope and the deployment context is India — ask if unclear |
| `references/dpdp.md` | The diagram handles personal data of individuals in India (customer PII, KYC/KYB data, user accounts, contact info) — broader trigger than `rbi.md`: applies to any India-facing consumer system, not just payment/financial ones |
| `references/csa-ccm.md` | The diagram shows cloud-hosted components (provider labels, managed services, VPC/cloud-account boundaries, SaaS third parties) |
| `references/owasp-api.md` | The diagram has an API Gateway, backend API, or service-to-service API calls (i.e., almost always) |
| `references/nist.md` (CSF) | Only when the user asks for a NIST-framed view, or the system looks US-federal/critical-infrastructure-adjacent — this one re-buckets findings you've already made elsewhere, so it's opt-in rather than default |

Given the user's PCI/EMV background, whenever PCI DSS is in scope, treat it as the primary lens and be specific about segmentation/CDE-boundary reasoning rather than generic.

**Consolidate before writing the findings table.** With this many frameworks, the same underlying architectural issue will legitimately map to several of them at once — e.g. "no auth on this internal flow" is simultaneously a STRIDE Spoofing note, a Zero Trust PEP gap, an OWASP API broken-authorization issue, and a CIS access-control gap; "no confirmed data-residency for the DR site" is simultaneously an RBI localisation question and a DPDP cross-border-transfer question. That is one finding, not four (or two). Write one row with all applicable standard references listed together (e.g. "STRIDE Spoofing / NIST 800-207 / OWASP API1 / CIS 6"), not one row per framework. A findings table with near-duplicate rows is exactly the noise failure mode described above — it's worse than a shorter table, because it buries the one or two things that only one framework caught.

## Step 5 — Severity

Rate each finding by actual exposure, not by framework count:

- **Critical** — internet-facing, touches cardholder/PII/credential data, no auth or encryption shown on that path
- **High** — crosses a trust boundary with a labeled data-sensitivity issue, or a CDE segmentation question
- **Medium** — internal-only but still crosses a meaningful boundary, or a control that's ambiguous/not shown rather than clearly absent
- **Low** — hardening/best-practice suggestions with no direct exposure shown

## Output format

ALWAYS use this structure:

```
# Architecture Security Review — [system name if known, else "Diagram Review"]

## Executive Summary
2-4 sentences: what the system does, biggest risk themes, overall posture.

## Architecture Inventory
[the table from Step 1]

## Trust Boundaries & Data Flows
[the list from Step 2]

## STRIDE Threat Notes
[concrete findings from Step 3, grouped by component/flow]

## Findings & Framework Mapping
[Table: Finding | Standard(s) & control ref | Severity | Recommendation]

## Open Questions / Not Shown in Diagram
[anything you couldn't assess because the diagram didn't show it — be explicit here rather than silently assuming]

## Prioritized Recommendations
1. [Critical/High items first, concrete next action]
```

Keep the framework-mapping table as the centerpiece — that's what turns this from a generic threat model into something the user can act on against a specific standard.

## Notes

- This is a design-time review based on what's drawn, not a runtime test — it can't catch anything the diagram doesn't represent (e.g., a missing WAF rule that isn't drawn as a component). Say this up front if the user seems to expect a penetration-test-level result.
- If the user uploads a second, revised diagram later in the same conversation, treat it as a delta: compare against the prior inventory and call out what changed and whether prior findings are now resolved.
