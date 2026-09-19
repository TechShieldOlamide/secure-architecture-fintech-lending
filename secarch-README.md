# Secure Architecture Design: Multi-Tenant Fintech Lending Platform

A security architecture design exercise for a fictional multi-tenant digital lending platform ("LendSecure"), built the way a security architect would approach a real system: design the architecture, model the threats against it, and map each threat to the specific compliance control that addresses it.

This is not a real company or system. It's modeled on the kind of platform I work with directly in my GRC and product security consulting (multi-tenant fintech, PII and financial data handling, third-party payment and KYC integrations).

## Why this exists

Most security documentation either shows an architecture diagram with no threat analysis, or a compliance checklist with no connection to the actual system it's supposed to protect. This project does both together: every control cited in `CONTROLS_MAPPING.md` maps to a specific threat identified against a specific component in `ARCHITECTURE.md`, not a generic framework requirement applied in the abstract.

## What's here

- **ARCHITECTURE.md** — system architecture and data flow diagrams (rendered as Mermaid), with reasoning for key design decisions
- **THREAT_MODEL.md** — a STRIDE-based threat model applied to each major component
- **CONTROLS_MAPPING.md** — each identified threat mapped to the specific ISO/IEC 27001 Annex A control (or SOC 2 Trust Services Criterion) that mitigates it

## Scenario

LendSecure is a B2B2C digital lending platform: lending businesses (tenants) use it to originate, underwrite, and service loans for their own customers. The platform handles:

- Customer PII (name, national ID, contact details)
- Financial data (income, loan amount, repayment history)
- KYC documents (ID uploads, proof of address)
- Payment processing (disbursement and repayment via a third-party processor)
- Multi-tenant data, meaning strict tenant isolation is a core security requirement, not an afterthought

## Key design decisions

- **Tenant isolation is enforced at the data layer**, not just the application layer, since application-layer bugs are the most common cause of cross-tenant data leaks in multi-tenant SaaS.
- **KYC documents and PII are stored separately from operational/transactional data**, so a compromise of one doesn't automatically expose the other.
- **Third-party integrations (payment processor, credit bureau, KYC provider) are treated as untrusted boundaries**, every integration point gets its own threat analysis rather than being assumed safe because it's a reputable vendor.
