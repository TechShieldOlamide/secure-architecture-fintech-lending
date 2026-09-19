# DORA Gap Assessment: LendSecure Platform

A gap assessment applying the EU Digital Operational Resilience Act (Regulation (EU) 2022/2554, in force since 17 January 2025) to the LendSecure architecture from this repository, framed as if LendSecure were expanding to serve EU-regulated financial institution clients (banks, credit institutions, or payment service providers), which would bring it into DORA's scope as an ICT third-party service provider.

This follows the same approach as `CONTROLS_MAPPING.md`: each DORA requirement is assessed against the actual architecture, not treated as an abstract checklist.

## Why This Matters for LendSecure Specifically

DORA applies directly to EU financial entities, but it also reaches **ICT third-party providers that serve them**, which is exactly LendSecure's position if any of its lending-partner tenants are EU-regulated banks or payment institutions. In that scenario, LendSecure would be a "critical or important ICT third-party service provider" under DORA Article 28, meaning its EU financial-institution clients are contractually required to assess and monitor LendSecure's resilience, not just take it on trust.

## The Five Pillars, Applied

### Pillar 1: ICT Risk Management

**Requirement:** A board-approved ICT risk management framework, with defined risk tolerance, roles, and regular review.

**Current state:** LendSecure's threat model (`THREAT_MODEL.md`) and controls mapping already provide the technical foundation, 13 identified threats, mapped controls, ISO 27001 alignment. What's missing for DORA specifically is the **governance layer**: a documented risk tolerance statement, named risk owners for each threat category, and evidence of board or senior-leadership review and approval.

**Gap:** Medium. The technical risk identification work exists; the formal governance wrapper around it (approval, ownership, review cadence) does not yet.

### Pillar 2: ICT-Related Incident Management, Classification and Reporting

**Requirement:** A documented incident response process, with a classification scheme for severity, and, critically, mandatory reporting to regulators within strict timeframes for major incidents.

**Current state:** The centralized audit log (`ARCHITECTURE.md`) feeding every service gives LendSecure the underlying data to detect and reconstruct an incident. There is no defined incident severity classification scheme, and no regulatory reporting workflow, DORA's incident reporting timelines are notably strict (initial notification within hours of classification as a major incident).

**Gap:** High. This is likely the single largest gap: logging infrastructure exists, but the process and classification framework that turns "we noticed something" into "we filed the right report with the right regulator in time" does not.

### Pillar 3: Digital Operational Resilience Testing

**Requirement:** A risk-based testing program, including for critical/important functions, threat-led penetration testing where applicable, conducted by independent testers.

**Current state:** The threat model in this repository is a structured analysis, but it is a design-time exercise, not live, adversarial testing of a running system. The AI security testing sandbox in this account (`ai-prompt-injection-testing-sandbox`) demonstrates the *methodology* of live adversarial testing, applied to a different system.

**Gap:** Medium-High. The organizational capability to design and run this kind of testing exists and is demonstrated; a live testing program against LendSecure's actual production environment does not yet exist.

### Pillar 4: ICT Third-Party Risk Management

**Requirement:** A register of all ICT third-party dependencies, risk assessment of each before contracting, and contractual provisions covering audit rights, termination, and exit strategies, particularly for "critical or important" providers.

**Current state:** LendSecure's architecture explicitly identifies three third-party integrations (KYC provider, credit bureau, payment processor) and `CONTROLS_MAPPING.md` already maps a control (A.5.19, supplier relationships) to the associated risk. This is a genuinely strong starting point.

**Gap:** Low-Medium. The risk identification exists; what's likely missing is the formal third-party register format DORA expects and documented exit strategies for each critical vendor, what happens if the payment processor needs to be replaced under time pressure.

### Pillar 5: Information and Intelligence Sharing

**Requirement:** Voluntary but encouraged participation in threat intelligence sharing arrangements with other financial entities.

**Current state:** No current mechanism for this exists in the architecture, this is a pillar with no direct technical control, it's an organizational and industry-relationship capability.

**Gap:** Not yet assessed as a priority; DORA treats this pillar as voluntary rather than mandatory, so it is reasonable to sequence it after the other four.

## Summary

| Pillar | Gap Level | Strongest Existing Asset |
|---|---|---|
| ICT Risk Management | Medium | Threat model + controls mapping (this repo) |
| Incident Management & Reporting | High | Centralized audit logging infrastructure |
| Resilience Testing | Medium-High | Demonstrated live-testing methodology (sandbox repo) |
| Third-Party Risk Management | Low-Medium | Existing supplier control mapping (A.5.19) |
| Information Sharing | Not prioritized | N/A |

## Why This Approach

DORA compliance work, like most regulatory frameworks, fails when it's treated as a document-writing exercise disconnected from the actual system. This assessment starts from the real architecture and threat model already built for this platform, and asks, pillar by pillar, what's already true, what's partially true, and what's a genuine gap, the same discipline used throughout this repository.
