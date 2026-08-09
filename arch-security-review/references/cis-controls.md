# CIS Critical Security Controls v8 — Architecture Review Lens

CIS Controls are practical and technical rather than governance-heavy — use this as the "did you actually do the basic thing" check that complements the more abstract ISO/NIST CSF lenses. Only the controls with real architectural signal are listed; skip CIS controls that are purely process/policy (e.g., security awareness training) since a diagram can't speak to those.

## What to look for in the diagram

**Inventory & control of assets (CIS 1, 2)**
- Can every component in the diagram actually be classified (owned service vs. third-party vs. unmanaged)? A diagram with vague/unlabeled boxes is itself a sign asset inventory maturity may be lacking — note this only if it's pervasive, not for one ambiguous box.

**Secure configuration (CIS 4)**
- Any component that looks like a default/managed service exposed without an intermediating control (e.g., a database or admin panel that appears directly reachable rather than behind a bastion/gateway).

**Account & access control management (CIS 5, 6)**
- Same lens as the access-control checks elsewhere (Auth/IAM component present, admin paths separated) — CIS adds an emphasis on distinct admin accounts and MFA on administrative access specifically. If an admin/management path is drawn, check whether it's differentiated from regular user access at all, and note if MFA isn't indicated on it.

**Data protection (CIS 3)**
- Same lens as PCI Req 3/4 and ISO cryptography, but broadened beyond cardholder data to any sensitive data labeled in the diagram (PII, credentials, health data, internal secrets).

**Network monitoring and defense (CIS 12, 13)**
- Segmentation between zones (same as PCI/ISO/ZTA lenses) plus: is there any monitoring/IDS component positioned at zone boundaries specifically, versus only centralized logging downstream? These are different controls — boundary monitoring catches things centralized log review might miss until later.

**Incident response management (CIS 17)**
- Rarely visible in a diagram directly — note as "not assessable from this diagram" like the NIST Respond function, unless there's an explicit alerting/incident-response integration shown.

**Penetration testing (CIS 18)**
- Not diagram-assessable — skip, don't manufacture a finding here.

## Severity guidance specific to this lens
- Admin/management path with no differentiation from user path and no MFA indicated → High
- Data-sensitive component reachable without an intermediating control → High
- No boundary-level monitoring (only centralized/downstream logging) → Medium
- Vague/unclassifiable components (pervasive, not isolated) → Low, framed as a maturity note rather than a per-component finding
