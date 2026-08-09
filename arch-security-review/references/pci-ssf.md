# PCI Secure Software Framework (PCI SSF) — Architecture Review Lens

PCI DSS (`pci-dss.md`) is about the *environment* around payment data — network segmentation, who can reach the CDE. PCI SSF is about the *payment application itself* — its internal architecture. Load this file only when the diagram shows the internals of a payment application/service (e.g., a Payment Service broken into sub-components, an EMV kernel, a POS/terminal application, tokenization logic) rather than just treating "Payment Service" as one opaque box. If the diagram only shows payment components as black boxes, PCI DSS's environment-level lens is the right (and sufficient) one — don't force SSF findings out of nothing.

## What to look for in the diagram

**Critical asset identification within the payment application**
- Within the payment component's internals (if shown): are the pieces that handle PAN, cryptographic keys, and authentication data visually distinguishable from general application logic? SSF expects the software vendor to know precisely which internal modules are "critical assets" — a diagram that treats the whole payment service as one undifferentiated block can't demonstrate this.

**Sensitive data lifecycle within the application**
- If sub-components are shown (e.g., input handling → validation → tokenization → transmission), does PAN appear to be scoped down (tokenized/truncated) as early as possible in that flow, or does it appear to persist across many internal steps unnecessarily?

**Cryptographic architecture**
- Same lens as PCI DSS's key-management check, but at the module level: is there a distinct crypto/HSM-interfacing module, or does encryption appear to happen ad hoc wherever PAN is touched? Centralized crypto handling is what SSF is looking for architecturally.

**Attack detection / self-defense within the application**
- Is there any indication of application-level integrity checks (e.g., anti-tampering, secure boot for a terminal/kernel component)? Relevant mainly for POS/EMV-kernel-style diagrams rather than typical web payment services — don't force this onto a generic backend payment microservice.

**Update/patch architecture**
- If the diagram shows a terminal or embedded component (as opposed to a centrally-updated backend service), is there any update-delivery path shown? Absence is worth noting as an open question for that component type specifically.

## Severity guidance specific to this lens
- PAN persisting across many internal modules with no visible tokenization/truncation point → High
- No distinct crypto-handling module, encryption implied ad hoc → Medium-High
- Terminal/embedded payment component with no visible update path → Medium
- Only applicable when payment-app internals are actually drawn — if not, don't manufacture findings here; rely on `pci-dss.md` instead
