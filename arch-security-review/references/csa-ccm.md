# CSA Cloud Controls Matrix v4.1 — Architecture Review Lens

Load this only when the diagram actually shows cloud-hosted components (explicit cloud provider labels, managed services like S3/RDS/Lambda-style boxes, "VPC"/"cloud account" boundaries, or SaaS third parties). For a purely on-prem diagram, skip this file entirely — it won't add anything ISO 27001 doesn't already cover.

## What to look for in the diagram

**Shared Security Responsibility Model (SSRM)**
- For every managed cloud service shown (managed DB, managed queue, serverless function, etc.), the provider secures the underlying infrastructure but the user is responsible for configuration, access, and data. The diagram itself won't show misconfiguration, but note explicitly which components fall under shared responsibility so the user knows a config review (not just this diagram review) is still needed for those.

**Identity & Access Management (CCM domain: IAM)**
- Is there a cloud-native IAM boundary (cloud account/project separation) distinct from the application-level Auth Service already covered elsewhere? Mixing workloads of different sensitivity in one cloud account/project with no separation is a CCM-specific finding distinct from the app-level access control findings.

**Infrastructure & Virtualization Security (CCM domain)**
- Multi-account/multi-project separation between environments (dev/test/prod) — same idea as the ISO 27001 environment-isolation check, but specifically about cloud account/project boundaries rather than network zones.

**Data Security & Privacy (CCM domain)**
- Data residency — if the diagram indicates a specific cloud region or if the user's context implies data-residency requirements (e.g., RBI data localisation expectations for payment data), note whether the diagram shows anything about region/locality. Don't assume a region if it isn't shown.

**Logging & Monitoring (CCM domain)**
- Cloud-native logging (e.g., a cloud audit-log service) distinct from the application SIEM already covered — cloud control-plane actions (who changed a security group, who touched IAM) need their own audit trail separate from application logs.

**Supply Chain Management, Transparency & Accountability (CCM domain)**
- Any third-party SaaS/API dependency hosted outside the user's own cloud boundary — same as the general third-party note in the ISO/PCI lenses, but flag specifically if that dependency also has access to the primary cloud environment (e.g., via a shared IAM role) rather than just an API call.

## Severity guidance specific to this lens
- Workloads of clearly different sensitivity sharing one cloud account/project with no separation → High
- No cloud control-plane audit logging indicated → Medium
- Third-party SaaS with a shared IAM role/access into the primary environment (rather than just an API call) → High
- Region/data-residency not indicated where it plausibly matters → note as Open Question, not a severity-rated finding, since it may simply not be drawn
