# NIST CSF 2.0 — Architecture Review Lens

Maps diagram findings onto the five CSF functions. Use this framing when the user wants a NIST-style view, or as a second cut over findings you already derived from the PCI/ISO lenses (the underlying findings are often the same; this just re-buckets them).

## Function mapping

**Identify** — Does the diagram itself demonstrate asset/data awareness? A diagram with labeled data classifications and clear ownership boxes is itself evidence of "Identify" maturity; a diagram with generic unlabeled boxes suggests a gap here before you even get to controls.

**Protect** — This is where most of your Step 1-3 findings land: access control, encryption, segmentation, secrets management — same checks as the PCI/ISO lenses, bucketed here.

**Detect** — Logging, monitoring, IDS/IPS, anomaly detection components. If none are visible anywhere in the diagram, that's a standalone Detect-function gap worth its own line, not just folded into a Protect finding.

**Respond** — Rarely visible in an architecture diagram directly (it's usually a process, not a component) — note as "not assessable from this diagram" rather than guessing, unless there's an explicit incident-response/alerting integration shown.

**Recover** — Backup/DR components, redundancy, multi-region — same as the ISO resilience lens. If nothing in the diagram indicates backup or recovery, note it as a Recover-function gap.

## Severity guidance specific to NIST framing
- Use the same severity levels as elsewhere (Critical/High/Medium/Low per the main SKILL.md rubric) — NIST here is a re-organization of findings by function, not a separate severity scale.
- A total absence of anything in Detect or Recover is worth flagging as a standalone summary line even if no individual component triggered a Critical/High elsewhere, since it changes the overall posture statement in the Executive Summary.
