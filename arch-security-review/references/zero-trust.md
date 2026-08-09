# NIST SP 800-207 — Zero Trust Architecture Lens

This is the most directly architectural standard in the set — it's fundamentally about trust boundaries and how access decisions are made, which is already the core of Step 2/3 of this review. Use it to sharpen those steps rather than as a separate bolt-on section.

## What to look for in the diagram

**Implicit trust zones (the core ZTA complaint)**
- Any place where being "inside" a boundary (DMZ, internal network, VPC) appears to be the only thing granting access — i.e., a flow with no auth/policy check shown once it's past the perimeter. ZTA's whole premise is that network location should not imply trust; flag every internal flow that looks like it trusts network position alone.
- Look specifically at east-west (service-to-service) traffic, not just north-south (client-to-server) — this is where implicit trust usually hides in a diagram (e.g., "internal call" arrows with no policy enforcement point drawn).

**Policy Enforcement Points (PEPs)**
- Does every sensitive resource have something in front of it that looks like a policy decision point (auth gateway, service mesh sidecar, reverse proxy with authz) — or does traffic appear to go straight from one component to another?
- A single perimeter PEP (e.g., only the API Gateway enforces anything) with nothing checked again internally is itself a finding — ZTA calls for per-resource enforcement, not just perimeter enforcement.

**Per-session, per-request authorization**
- Is authorization shown as a one-time thing (login once, then trusted for the rest of the session/architecture) or continuously re-evaluated per request? Diagrams rarely show this explicitly — if not shown, note as an open question rather than assuming either way.

**Microsegmentation**
- Compare the boundary crossings from Step 2 against the zones drawn. If the zones are coarse (just "DMZ" and "Internal") rather than segmented per-service or per-data-sensitivity, that's a segmentation-maturity gap worth naming, distinct from (but related to) the PCI CDE segmentation finding.

**Device/workload identity**
- Is there any indication of service-to-service identity (mTLS, workload certs, service accounts) versus components just trusting whatever calls them? Absence is common and worth flagging once, not per-flow.

## Severity guidance specific to this lens
- Sensitive resource reachable via network position alone, no PEP shown → High (this often overlaps with the STRIDE Spoofing/EoP findings — consolidate rather than double-count, see Step 4/5 consolidation note in SKILL.md)
- Only perimeter enforcement, nothing internal → Medium
- No service-to-service identity/mTLS indicated → Low-Medium depending on what crosses that path
