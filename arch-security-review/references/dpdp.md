# DPDP Act, 2023 & DPDP Rules, 2025 — Architecture Review Lens (India)

Load this whenever the diagram handles personal data of individuals in India — customer PII, KYC/KYB data, user accounts, contact/biometric info, app usage data — which in practice means almost any consumer-facing Indian system, not just payment ones. This is distinct from `rbi.md`: RBI's lens is about financial-sector risk and regulatory reporting; DPDP is about personal-data rights and obligations broadly. The two overlap heavily on breach-notification and cross-border-transfer questions (don't duplicate those findings — see consolidation note in SKILL.md) but DPDP's scope is wider: it applies to Auth Service, Notification Service, KYC/KYB integrations, and any component touching a customer's personal data, not only Payment/Wallet Service.

This is a live-rollout regulation: the DPDP Rules, 2025 were notified in November 2025 with a phased timeline (Board and penalties active immediately, Consent Manager framework operational around November 2026, full compliance obligations by May 2027 — treat these dates as the current baseline and flag to the user that they should confirm the live timeline for a real assessment, since the compliance window has already been the subject of proposed acceleration). Given the phased rollout, an "internal assessment" review under this lens should read as **build-readiness gap-finding**, not a claim that the diagram is currently out of compliance.

## What to look for in the diagram

**Consent capture and management**
- Is there any component that looks like a consent-tracking or Consent Manager integration point (as opposed to consent handling being implicit/undrawn)? Its absence isn't necessarily wrong at prototype stage, but it's a real gap once the Consent Manager framework becomes operational — note it as a forward-looking open item rather than a current-state Critical.

**Purpose limitation & data minimization**
- Does personal data collected by one component (e.g., KYC/KYB Provider integration) appear to flow only to the components that need it for a stated purpose, or does it look broadly accessible across services? This is the same lens as the Information Disclosure/CCM "overexposure to downstream components" check elsewhere — apply it specifically to personal data here, not just cardholder data.

**Data Principal rights servicing (access, correction, erasure, grievance)**
- Is there any visible mechanism (API, service, or grievance-officer touchpoint) for handling a data-subject request, or does the diagram only show data flowing in, never a path for a user to reach it back out? Absence is common at this stage — flag as an Open Question / roadmap item, not an assumed violation.

**Security safeguards (Rule-level requirement)**
- The Rules require "reasonable security safeguards" — encryption, access control, access logging, monitoring, and backups — explicitly extending to Data Processors acting on the fiduciary's behalf, not just the fiduciary itself. This is largely the same evidence you're already gathering for ISO/CIS/PCI; the DPDP-specific angle is whether that same rigor is confirmed for third-party processors (KYC/KYB Providers, any outsourced component) rather than assumed to apply only internally.

**Breach notification**
- DPDP requires notifying both the Data Protection Board and affected individuals, on a tighter clock than most organizations' existing incident-response processes were built for. If an Incident Response/SIEM component is shown, note as an Open Question whether its process extends to individual-level notification, not just internal alerting — this is the same underlying gap as the RBI incident-reporting note where both apply; state once, tag both.

**Cross-border data transfer**
- The Act allows the government to restrict transfers to specific countries (a blacklist model) rather than a blanket localization requirement. If the diagram shows any component, backup, or DR site outside India (or doesn't specify), flag as an Open Question on both this file and `rbi.md` together — one finding, two tags, not two findings.

**Significant Data Fiduciary (SDF) obligations**
- DPIAs, a data protection officer, and independent audits apply to entities the government designates as Significant Data Fiduciaries (typically based on data volume/sensitivity). Not diagram-assessable — note in Open Questions whether the org's expected scale/data sensitivity would put it in SDF territory, since that changes which of the above become mandatory versus best-practice.

## Severity guidance specific to this lens
- Personal data from a third-party processor (KYC/KYB) with no visible boundary/access-control distinguishing it from internally-collected data → Medium
- No visible data-subject-request handling path anywhere in the architecture → Medium, framed as a build-readiness gap given the phased timeline, not a current violation
- No consent-tracking component → Low-Medium currently, rising as the Consent Manager deadline approaches — note the timeline dependency explicitly
- Cross-border transfer/data-residency not indicated → Open Question (shared with `rbi.md` where both apply)
