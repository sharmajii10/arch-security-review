# OWASP API Security Top 10 — Architecture Review Lens

Load this whenever the diagram has an API Gateway, backend API, or service-to-service API calls (i.e., almost always). This is a sharper complement to the general ASVS lens in `stride-owasp.md` — ASVS covers auth/session/crypto placement broadly, this focuses specifically on API-shaped risks. Like everything else in this skill, only flag what the diagram gives you evidence for — most of these need an object/data model you won't have from a diagram alone, so most findings here will be "check this" rather than "this is broken."

## What to look for in the diagram

**Broken Object Level / Function Level Authorization (API1, API5)**
- Does every API component sit behind the Auth Service, or do some (like the Orders-Service-style pattern) appear reachable directly from the gateway without a shown authorization step? This is the same Spoofing/EoP signal as the STRIDE and Zero-Trust lenses — consolidate into one finding, don't triple-count it.

**Broken Authentication (API2)**
- Is authentication centralized (one Auth Service/IdP) or does each API appear to roll its own? Duplicated/inconsistent auth implementations across services is itself worth flagging even without knowing the implementation details.

**Unrestricted Resource Consumption (API4)**
- Same as the STRIDE DoS check — rate-limiting/gateway component in front of public APIs. Don't duplicate the finding; this file just confirms it's also an API-specific concern, not just a general DoS one.

**Server-Side Request Forgery (API7)**
- Any API component that itself makes outbound calls based on user-supplied input (e.g., a "fetch URL"/webhook/callback feature) — flag as worth checking if such a component is visible, since SSRF specifically needs this pattern.

**Improper Inventory Management (API9)**
- Is there any indication of API versioning or a clear boundary of what's "current" vs. "legacy" (e.g., a component labeled with a version, or multiple similar-looking API boxes)? Multiple overlapping/undifferentiated API surfaces is itself a finding — shadow/zombie APIs are a common real-world source of breaches precisely because they're undocumented, which a messy diagram can hint at.

**Unsafe Consumption of APIs (API10)**
- Any inbound flow from a third-party API into the system with no validation/gateway step shown before it reaches internal components — the system implicitly trusting third-party data the same way it might implicitly trust internal callers.

## Severity guidance specific to this lens
- Public API with no auth and no rate-limiting shown → Critical (this is very likely the same underlying flow as a Critical finding elsewhere — consolidate)
- Duplicated/inconsistent auth across services → Medium
- Multiple undifferentiated/versionless API surfaces → Medium
- Third-party API input consumed with no validation step shown → Medium-High depending on what it feeds into
