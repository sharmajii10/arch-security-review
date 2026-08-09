# RBI Cybersecurity & Digital Payment Regulatory Lens (India)

Load this whenever the diagram is for a bank, NBFC, payment aggregator/gateway, card network participant, or any other RBI-regulated entity operating in India — or whenever cardholder/payment data is in scope and the deployment context is India (ask if unclear rather than assuming). This is a live regulatory area: RBI has been actively issuing and revising directions through 2025–2026, including a draft Digital Payment Security Controls update circulating in 2026. Treat the specific requirements below as the current thematic baseline, not a frozen checklist — flag to the user that the exact circular in force should be verified for a real assessment, especially anything time-sensitive like authentication or incident-reporting deadlines.

This lens overlaps somewhat with PCI DSS and ISO 27001 (segmentation, encryption, access control) — don't restate the same finding under a different name. Use this file specifically for the things that are RBI-specific and wouldn't be caught by the international standards.

## Relevant instruments (by theme, not exhaustive)

- **Governance**: Master Direction on IT Governance, Risk, Controls and Assurance Practices — board-level IT Strategy Committee, board-approved IT risk framework, mandatory Cyber Crisis Management Plan (CCMP)
- **Digital payment application security**: Master Direction on Digital Payment Security Controls — internet banking, mobile banking app, and card payment security controls specifically
- **Authentication**: RBI directions on authentication mechanisms for digital payment transactions — dynamic, transaction-linked two-factor authentication (not a static/reusable OTP alone) for non-recurring payments, with risk-based step-up for unusual device/location/amount patterns
- **Third-party/outsourcing**: Master Direction on Outsourcing of IT Services, and outsourcing of financial services more broadly — vendor risk management, exit clauses, concentration-risk monitoring
- **NBFC-specific**: Master Direction – Information Technology Framework for the NBFC Sector, where the entity in the diagram is an NBFC rather than a bank
- **Non-bank payment system operators**: Master Directions on Cyber Resilience and Digital Payment Security Controls for non-bank PSOs — applies to payment aggregators, card networks, PPI issuers specifically
- **Incident reporting**: significant cyber incidents expected to be reported to RBI/CERT-In within a tight window (recent tightening toward same-day/hours-scale reporting) — a diagram can't show this, but it's worth a line in Open Questions asking whether an incident-reporting/alerting path exists

## What to look for in the diagram

**Board/governance visibility** — not diagram-assessable directly; skip rather than manufacture a finding, but you can note in Open Questions whether a CCMP/incident-response integration is shown anywhere (ties to the incident-reporting theme above).

**Dynamic/transaction-linked authentication** — for any payment-initiation flow, is the auth step shown as something that binds to that specific transaction (amount, payee, dynamic factor), or does it look like a generic session-level login that, once done, authorizes everything downstream? The latter is the pattern the newer authentication directions are specifically pushing away from.

**Data localisation** — if the diagram shows where payment data is stored/processed (region, "outside India" labels, or a foreign cloud region), flag as an Open Question whether payment-system data localisation requirements apply to that entity type and whether the shown location satisfies them — don't assert compliance either way from a diagram alone.

**Segregation for card payment security controls** — same segmentation lens as PCI DSS, but note if the diagram is for a bank/NBFC specifically, since RBI's DPSC directions layer on top of (not instead of) PCI DSS for entities that are also card-scheme participants.

**Outsourced/third-party components** — any vendor or outsourced component (payment gateway, cloud host, BPO-operated support system) — flag it against the outsourcing theme above, distinct from the generic PCI/ISO third-party note, since RBI outsourcing rules are more prescriptive about exit planning and concentration risk.

## Severity guidance specific to this lens
- Payment-initiation flow with only session-level auth, no transaction-binding factor shown → High
- Payment data location/region not indicated where localisation plausibly applies → Open Question, not a rated finding
- No indication of an incident-reporting/CCMP integration anywhere in the diagram → Medium, framed as a governance gap rather than a technical one
- Outsourced/vendor component with no visible boundary control distinguishing it from in-house components → Medium
