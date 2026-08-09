# PCI DSS v4.0 — Architecture Review Lens

Original guidance for mapping diagram findings to PCI DSS themes. This is not the standard's text — treat it as a lens for what to look for, and cite requirement numbers only at the level of "Req X.Y area" rather than quoting requirement language.

## What to look for in the diagram

**CDE scope & segmentation (Req 1, 11.4)**
- Is there a clear boundary around components that store/process/transmit PAN or track data (the CDE)?
- Do any flows cross from outside the CDE into it, or from the CDE outward, without a firewall/segmentation control shown?
- Are "connected-to" systems (systems that touch the CDE but don't store cardholder data — e.g. a monitoring agent, a shared AD) drawn as in-scope? If a component has any path into the CDE, treat it as in-scope even if unlabeled.
- Flat/undifferentiated networks (everything in one box, no internal boundaries) are themselves a finding — segmentation is what limits PCI scope.

**Data protection (Req 3, 4)**
- Any component storing PAN: is encryption-at-rest indicated? Is there a separate key management component, or is that not shown (flag as open question, don't assume KMS exists)?
- Any flow carrying PAN: is TLS/encryption-in-transit labeled? Flows without a protocol label crossing a boundary are a finding, not an assumption of plaintext — phrase as "encryption not shown, verify."
- Look for tokenization/vaulting — a separate "token vault" or "payment gateway" component reduces PAN sprawl; note it as a positive if present.

**Access control (Req 7, 8)**
- Is there an identity/auth component (SSO, IAM) gating access into the CDE, or do flows appear to go direct?
- Any admin/management path into CDE components — is it separated from user-facing paths?

**Logging & monitoring (Req 10, 11)**
- Is there a logging/SIEM component collecting from CDE systems? Absence is a finding if not shown.
- Any IDS/IPS or file-integrity component at the CDE boundary?

**Third parties (Req 12, and shared-responsibility generally)**
- Any external/third-party service touching cardholder data (payment processor, fraud service, cloud provider) — note it as a shared-responsibility boundary; the review should call out that a contract/AOC review is needed, not assess the third party itself.

## Severity guidance specific to PCI
- A boundary crossing into/out of the CDE with no segmentation control shown → Critical
- PAN storage with no encryption or key management shown → Critical
- CDE flow with no encryption-in-transit label → High
- Missing logging/monitoring on CDE components → High
- Flat network containing CDE and non-CDE systems together → High
