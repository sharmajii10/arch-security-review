# STRIDE → OWASP ASVS Architecture Lens

Use this to turn Step 3's STRIDE notes into concrete, ASVS-style architecture verification points. This is about where/how controls should sit architecturally, not code-level review.

## STRIDE → architectural question

- **Spoofing** → Is there a clear authentication component/mechanism on every flow that crosses a trust boundary? A flow with no visible auth crossing into a sensitive component is the concrete finding — name the specific flow.
- **Tampering** → For flows carrying data that matters (payment amounts, permissions, config), is integrity protection (signing, TLS, checksums) implied? Absence on an internal-only flow is lower severity than on an internet-facing one.
- **Repudiation** → Is there a logging/audit component positioned to capture who-did-what for sensitive actions (payment initiation, admin changes)? If the only logging shown is infrastructure-level (not action-level), note that as a gap.
- **Information Disclosure** → Trace every flow that touches sensitive data outward — does it cross into a less-trusted zone (e.g., sensitive data flowing to a logging/analytics/third-party component that doesn't need it)? Overexposure to downstream components is a common real finding.
- **Denial of Service** → Are there rate-limiting/WAF/gateway components in front of internet-facing services, or does traffic appear to hit backend services directly?
- **Elevation of Privilege** → Is there a single component whose compromise would grant broad access (an over-privileged service account, a shared admin path)? Flag concentration of privilege as its own finding.

## Architecture-level ASVS themes to check
- Authentication and session components are centralized rather than duplicated per-service
- Sensitive operations (payment, admin, data export) have a distinguishable, auditable path — not indistinguishable from routine reads
- Input validation/WAF sits at the boundary before requests reach application logic
- Secrets (API keys, DB credentials) flow through a secrets manager component rather than appearing to be embedded in application components (only note this if the diagram actually shows or implies hard-coded config — don't assume)

## Severity guidance specific to this lens
- No auth component on an internet-facing entry point → Critical
- No rate-limiting/gateway in front of a public API → High
- Concentration of privilege in one component with broad downstream access → High
- Sensitive data flowing to a component that doesn't clearly need it → Medium
