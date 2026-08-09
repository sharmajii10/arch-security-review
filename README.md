# arch-security-review

A [Claude Skill](https://www.anthropic.com/news/skills) that turns an uploaded architecture diagram into a structured security architecture review — mapped against **11 standards**: PCI DSS, PCI Secure Software Framework, ISO 27001, NIST CSF, NIST SP 800-207 (Zero Trust), CIS Controls v8, CSA Cloud Controls Matrix, OWASP ASVS, OWASP API Security Top 10, STRIDE, and India's DPDP Act/Rules.

You give it a diagram (screenshot, exported draw.io/Lucidchart/Visio image, even a photo of a whiteboard). It gives you back:

- A component inventory
- Trust boundaries and data flows
- A STRIDE threat pass
- Findings mapped to whichever standards are actually relevant — it doesn't force all 11 onto every diagram, only the ones the diagram gives it evidence for (e.g. PCI DSS only loads if cardholder data is labeled, CSA CCM only loads if the diagram shows cloud-hosted components, RBI/DPDP only load for India-context systems)
- One consolidated findings table with severity ratings, not a separate row per framework for the same underlying issue
- An explicit "Open Questions" section for anything the diagram doesn't show — it's built to say "not shown" rather than assume a control exists because a well-designed system would have it

## Why

Most AI-assisted architecture reviews either hallucinate confidence (assuming encryption/auth that isn't actually labeled) or produce noise (flagging every theoretical issue regardless of what's in the diagram). This skill is built around avoiding both failure modes — see the "Why the process matters" section in [SKILL.md](arch-security-review/SKILL.md) for the actual discipline it follows.

## Install

**claude.ai** (Pro/Max/Team/Enterprise, with code execution enabled):
1. Download [`arch-security-review.skill`](arch-security-review.skill) (or zip the `arch-security-review/` folder yourself)
2. Settings → Capabilities → upload the file
3. Enable it

**Claude Code**:
```bash
git clone https://github.com/sharmajii10/arch-security-review.git
cp -r arch-security-review/arch-security-review ~/.claude/skills/
```
Or drop the `arch-security-review/` folder into a project's `.claude/skills/` to share it with your whole team via the repo.

## Use

Upload an architecture diagram and ask for a security review, threat model, or compliance gap analysis — you don't need to name a specific standard, the skill figures out which ones apply from what's actually in the diagram.

## Structure

```
arch-security-review/
├── SKILL.md              # workflow: inventory → trust boundaries → STRIDE → framework mapping → severity
└── references/
    ├── pci-dss.md
    ├── pci-ssf.md
    ├── iso27001.md
    ├── nist.md            # NIST CSF 2.0
    ├── zero-trust.md       # NIST SP 800-207
    ├── cis-controls.md     # CIS Controls v8
    ├── csa-ccm.md          # CSA Cloud Controls Matrix v4.1
    ├── stride-owasp.md      # STRIDE + OWASP ASVS
    ├── owasp-api.md         # OWASP API Security Top 10
    ├── rbi.md               # RBI cybersecurity/digital-payment regulations (India)
    └── dpdp.md               # India's DPDP Act & Rules (privacy)
```

Each reference file only loads when the diagram gives it a reason to — see the routing table in `SKILL.md`.

## Disclaimer

This is a starting point for a review, not a substitute for a qualified assessor or an actual audit. Treat findings as things to verify, not as certified results — especially anything touching PCI scope, RBI regulatory alignment, or DPDP compliance, all of which have real consequences if gotten wrong on an actual production system.

## Contributing

Issues and PRs welcome — additional standards (NPCI-specific requirements, SWIFT CSP, sector-specific frameworks), sharper severity calibration, or corrections to anything that's aged out (the RBI and DPDP sections in particular cover live-rollout regulations that will keep changing).

## License

MIT — see [LICENSE](LICENSE).
